# Repro for bug in ASP.NET 6 RC2

The bug was already identified and apparently fixed as for https://github.com/dotnet/aspnetcore/issues/35285. Unfortunately it is still present at the current date (Nov 8, 2021) in RC2 (6.0.0-rc.2.21480.5).

Steps to recreate the bug:

- Create a new ASP.NET Core (Razor web pages) project using .NET 5 and the template provided in VS2022 Preview 7

- Change the csproj file to `net6.0`

  ```
  <TargetFramework>net6.0</TargetFramework>
  ```

- On Windows, the issue is not present

- Publish the project using:

  ```
  dotnet build -c Release -r linux-arm --self-contained
  ```

- Copy the binary files to a Linux machine (I used an ARM processor)

If the hosting environment is `Production` the application works as expected

Now switch to `Development`:

```
export ASPNETCORE_ENVIRONMENT=Development
```

The error now shows up:

```
~/test6 $ ./PathMustBeAbsolute
Unhandled exception. System.ArgumentException: The path must be absolute. (Parameter 'root')
   at Microsoft.Extensions.FileProviders.PhysicalFileProvider..ctor(String root, ExclusionFilters filters)
   at Microsoft.Extensions.FileProviders.PhysicalFileProvider..ctor(String root)
   at Microsoft.AspNetCore.Hosting.StaticWebAssets.StaticWebAssetsLoader.<>c.<UseStaticWebAssetsCore>b__1_0(String contentRoot)
   at Microsoft.AspNetCore.StaticWebAssets.ManifestStaticWebAssetFileProvider..ctor(StaticWebAssetManifest manifest, Func`2 fileProviderFactory)
   at Microsoft.AspNetCore.Hosting.StaticWebAssets.StaticWebAssetsLoader.UseStaticWebAssetsCore(IWebHostEnvironment environment, Stream manifest)
   at Microsoft.AspNetCore.Hosting.StaticWebAssets.StaticWebAssetsLoader.UseStaticWebAssets(IWebHostEnvironment environment, IConfiguration configuration)
   at Microsoft.AspNetCore.WebHost.<>c.<ConfigureWebDefaults>b__9_0(WebHostBuilderContext ctx, IConfigurationBuilder cb)
   at Microsoft.AspNetCore.Hosting.GenericWebHostBuilder.<>c__DisplayClass9_0.<ConfigureAppConfiguration>b__0(HostBuilderContext context, IConfigurationBuilder builder)
   at Microsoft.Extensions.Hosting.HostBuilder.BuildAppConfiguration()
   at Microsoft.Extensions.Hosting.HostBuilder.Build()
   at PathMustBeAbsolute.Program.Main(String[] args) in H:\dev.git\_bugs\AspNet6Test\PathMustBeAbsolute\PathMustBeAbsolute\Program.cs:line 17
Aborted
```

