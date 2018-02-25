# laba2_SPOVM
process sincronization

#include <iostream>
#include <windows.h>
#define QUANTITY 800

DWORD CreateNewProcess (STARTUPINFO &startupInfo, PROCESS_INFORMATION &processInfo, TCHAR *commandLineParam);
void ClearMemory (STARTUPINFO &startupInfo, PROCESS_INFORMATION &processInfo);
BOOL CloseAllProcessObjects (PROCESS_INFORMATION &processInfo);
void PrintWord (const char* word);

using namespace std;

int main (int argc, TCHAR *argv[]) 
{
	const char* childProcess = "child";
	const char* parentProcess = "parent";

	STARTUPINFO StartupInfo = { sizeof(StartupInfo) };
	PROCESS_INFORMATION ProcessInfo;
	TCHAR CommandLine[] = "laba2";

	LPCSTR name = "Sincron";
	HANDLE event = CreateEvent(NULL, FALSE, FALSE, name);

	if (!strcmp(argv[0], "laba2"))
	{
		OpenEvent (EVENT_ALL_ACCESS, TRUE, name);
		PrintWord (childProcess);
		SetEvent(event);
		PrintWord(childProcess);
		system ("pause");
		return 0;
	}

	if (!CreateNewProcess (StartupInfo, ProcessInfo, CommandLine)) 
	{
		cout << "Create process failed" << endl;
		return 1;
	}

	PrintWord (parentProcess);
	WaitForSingleObject (event, INFINITE);
	PrintWord(parentProcess);
	CloseAllProcessObjects (ProcessInfo);

	system ("pause");
	return 0;
}

void PrintWord (const char* word) 
{
	static int count = 1;
	for (int i = 0; i < QUANTITY; i++) 
	{
		cout << word << " process  " << count << endl;
	}
	count++;
}

BOOL CloseAllProcessObjects (PROCESS_INFORMATION &processInfo) 
{
	return CloseHandle (processInfo.hProcess) &&
		CloseHandle (processInfo.hThread);
}

DWORD CreateNewProcess (STARTUPINFO &startupInfo, PROCESS_INFORMATION &processInfo, TCHAR *commandLineParam) 
{
	ClearMemory (startupInfo, processInfo);
	return CreateProcess(
		NULL,
		commandLineParam,
		NULL,
		NULL,
		TRUE,
		CREATE_NEW_CONSOLE,
		NULL,
		NULL,
		&startupInfo,
		&processInfo
	);
}

void ClearMemory (STARTUPINFO &startupInfo, PROCESS_INFORMATION &processInfo) 
{
	ZeroMemory (&startupInfo, sizeof(startupInfo));
	startupInfo.cb = sizeof(startupInfo);
	ZeroMemory (&processInfo, sizeof(processInfo));
}
