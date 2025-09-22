When a program references another service/file or program it shall use an absolute path. Failing that, Windows will try to resolve the assets specified in the current directory if the running program. If it is found, the asset will be used, otherwise it will use the original location.

## Example

```d
Process.Start("calc.exe");
```

*Assume working directory is C:\Users\chris\MyApp and my app is called MyApp*.

1. Run `MyApp.exe`
2. Put a malicious program in *C:\Users\chris\MyApp* and rename it to `calc.exe`
3. Use a functionality that will call `calc.exe`
4. Windows will look for `calc.exe` in the working directory.
5.  **Windows runs the user provided file**