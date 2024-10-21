# dependabotTest
test new dependabot sf update

## test 1 - a known good dependabot update
we have a single test.csproj that refs  2 packages.  those packages dont have any transitive dependencies in common
- Microsoft.ApplicationInsights" Version="2.13.0"   	  	  
- Microsoft.ServiceFabric.AspNetCore.Kestrel" Version="7.1.2338" 

- run dependabot on git hub.
- dependabot sucesfully creates a single PR called [Bump the dev-dependencies group across 1 directory with 2 updates #2]

pr changes. 
```
-		<PackageReference Include="Microsoft.ApplicationInsights" Version="2.13.0" />  	  	  	  
-		<PackageReference Include="Microsoft.ServiceFabric.AspNetCore.Kestrel" Version="7.1.2338" />  	  	  
+		<PackageReference Include="Microsoft.ApplicationInsights" Version="2.22.0" />  	  	  	  
+		<PackageReference Include="Microsoft.ServiceFabric.AspNetCore.Kestrel" Version="7.1.2448" />  	
```

## test 2 - dependabot update with 2 packages that share transitive dependent packages
- test.csproj project has 2 dependencies  
  - MicroSoft.serviceFabric.AspNetCore.Kestrel (7.1.2338 )
  - MicroSoft.ServiceFabric.Services.Remoting (7.1.2338)  

Both packages have a transitive dependency on **MicroSoft.serviceFabric.Services**

### expected behaviour
- we would like a single PR that updates both packages to 7.1.2448
- problem is that NugetUpdater.cli  updates 1 package at a time. 

### actual behaviour
- get the following error from the NugetUpdater.  full error message at end of file. 

```
error NU1107: Version conflict detected for Microsoft.ServiceFabric.Services. Install/reference Microsoft.ServiceFabric.Services 7.1.2448 directly to project Project to resolve this issue. 

 /tmp/package-dependency-resolution_SJq1wJ/Project.csproj : error NU1107:  Project -> Microsoft.ServiceFabric.AspNetCore.Kestrel 7.1.2448 -> Microsoft.ServiceFabric.AspNetCore.Abstractions 7.1.2448 -> Microsoft.ServiceFabric.Services (= 7.1.2448) 

 /tmp/package-dependency-resolution_SJq1wJ/Project.csproj : error NU1107:  Project -> Microsoft.ServiceFabric.Services.Remoting 7.1.2338 -> Microsoft.ServiceFabric.Services (= 7.1.2338).
```


## dependency chain #####
MicroSoft.serviceFabric.AspNetCore.Kestrel (7.1.2338 )
	-> MicroSoft.ServiceFabric.AspNetCore.Abstractions (7.1.2338 )
		-> MicroSoft.serviceFabric.Services (7.1.2338) **update to 7.1.2448 via kestrel conflicts with that in remoting chain ?? **
	
MicroSoft.ServiceFabric.Services.Remoting (7.1.2338)  
	->MicroSoft.serviceFabric.Services (7.1.2338) **
	->MicroSoft.serviceFabric.diagnostics.Internal
	->MicroSoft.ServiceFabric.FabricTransport.Internal (7.1.2338)

### sample of  dependabot logs with errors ###
much stuff ommited to save space. 
```
2024-10-20T17:00:47.1766176Z updater | running NuGet discovery:
2024-10-20T17:00:47.1768718Z /opt/nuget/NuGetUpdater/NuGetUpdater.Cli discover --repo-root /home/dependabot/dependabot-updater/repo --workspace / --output /tmp/.dependabot/discovery.1.json

{
   "Path": "",
   "IsSuccess": true,
   "Projects": [
     {
       "FilePath": "test.csproj",
       "Dependencies": [
         {
           "Name": "Microsoft.NET.Sdk",
           "Version": null,
           "Type": "MSBuildSdk",
           "EvaluationResult": null,
           "TargetFrameworks": null,
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": false,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric",
           "Version": "10.1.2338",
           "Type": "Unknown",
           "EvaluationResult": null,
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": true,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.AspNetCore.Abstractions",
           "Version": "7.1.2338",
           "Type": "Unknown",
           "EvaluationResult": null,
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": true,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.AspNetCore.Kestrel",
           "Version": "7.1.2338",
           "Type": "PackageReference",
           "EvaluationResult": {
             "ResultType": "Success",
             "OriginalValue": "7.1.2338",
             "EvaluatedValue": "7.1.2338",
             "RootPropertyName": null,
             "ErrorMessage": null
           },
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": true,
           "IsTransitive": false,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.Data",
           "Version": "7.1.2338",
           "Type": "Unknown",
           "EvaluationResult": null,
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": true,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.Data.Extensions",
           "Version": "7.1.2338",
           "Type": "Unknown",
           "EvaluationResult": null,
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": true,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.Data.Interfaces",
           "Version": "7.1.2338",
           "Type": "Unknown",
           "EvaluationResult": null,
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": true,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.Diagnostics.Internal",
           "Version": "7.1.2338",
           "Type": "Unknown",
           "EvaluationResult": null,
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": true,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.FabricTransport.Internal",
           "Version": "7.1.2338",
           "Type": "Unknown",
           "EvaluationResult": null,
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": true,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.Services",
           "Version": "7.1.2338",
           "Type": "Unknown",
           "EvaluationResult": null,
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": false,
           "IsTransitive": true,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         },
         {
           "Name": "Microsoft.ServiceFabric.Services.Remoting",
           "Version": "7.1.2338",
           "Type": "PackageReference",
           "EvaluationResult": {
             "ResultType": "Success",
             "OriginalValue": "7.1.2338",
             "EvaluatedValue": "7.1.2338",
             "RootPropertyName": null,
             "ErrorMessage": null
           },
           "TargetFrameworks": [
             "net8.0"
           ],
           "IsDevDependency": false,
           "IsDirect": true,
           "IsTransitive": false,
           "IsOverride": false,
           "IsUpdate": false,
           "InfoUrl": null
         }
       ],
       "IsSuccess": true,
       "Properties": [
         {
           "Name": "ImplicitUsings",
           "Value": "enable",
           "SourceFilePath": "test.csproj"
         },
         {
           "Name": "Nullable",
           "Value": "enable",
           "SourceFilePath": "test.csproj"
         },
         {
           "Name": "TargetFramework",
           "Value": "net8.0",
           "SourceFilePath": "test.csproj"
         }
       ],
       "TargetFrameworks": [
         "net8.0"
       ],
       "ReferencedProjectPaths": []
     }
   ],
   "DirectoryPackagesProps": null,
   "GlobalJson": null,
   "DotNetToolsJson": null,
   "ErrorType": null,
   "ErrorDetails": null
 }
 
 
 
 2024-10-20T17:01:08.2982508Z updater | 2024/10/20 17:01:08 INFO <job_903580535> Updating Microsoft.ServiceFabric.AspNetCore.Kestrel from 7.1.2338 to 7.1.2448
2024-10-20T17:01:08.2994940Z updater | running NuGet updater:
2024-10-20T17:01:08.2998561Z /opt/nuget/NuGetUpdater/NuGetUpdater.Cli update --repo-root /home/dependabot/dependabot-updater/repo --solution-or-project /home/dependabot/dependabot-updater/repo/test.csproj --dependency Microsoft.ServiceFabric.AspNetCore.Kestrel --new-version 7.1.2448 --previous-version 7.1.2338 --result-output-path /tmp/update-result.json
....
2024-10-20T17:01:40.2953303Z   No global.json file found.
2024-10-20T17:01:40.2954223Z Running for project file [test.csproj]
2024-10-20T17:01:40.2955333Z Updating project [/home/dependabot/dependabot-updater/repo/test.csproj]
2024-10-20T17:01:40.2956598Z   Running 'PackageReference' project direct XML update
2024-10-20T17:01:40.2957883Z     Found incorrect [PackageReference] version attribute in [test.csproj].
2024-10-20T17:01:40.2959464Z dotnet build in GetAllPackageDependenciesAsync failed. STDOUT:   Determining projects to restore...
2024-10-20T17:01:40.2961853Z /tmp/package-dependency-resolution_SJq1wJ/Project.csproj : warning NU1900: Error occurred while getting package vulnerability data: Unable to load the service index for source https://pkgs.dev.azure.com/visiontrack/_packaging/visiontrack/nuget/v3/index.json.
2024-10-20T17:01:40.2964410Z /tmp/package-dependency-resolution_SJq1wJ/Project.csproj : error NU1107: Version conflict detected for Microsoft.ServiceFabric.Services. Install/reference Microsoft.ServiceFabric.Services 7.1.2448 directly to project Project to resolve this issue. 
2024-10-20T17:01:40.2967172Z /tmp/package-dependency-resolution_SJq1wJ/Project.csproj : error NU1107:  Project -> Microsoft.ServiceFabric.AspNetCore.Kestrel 7.1.2448 -> Microsoft.ServiceFabric.AspNetCore.Abstractions 7.1.2448 -> Microsoft.ServiceFabric.Services (= 7.1.2448) 
2024-10-20T17:01:40.2969406Z /tmp/package-dependency-resolution_SJq1wJ/Project.csproj : error NU1107:  Project -> Microsoft.ServiceFabric.Services.Remoting 7.1.2338 -> Microsoft.ServiceFabric.Services (= 7.1.2338).
2024-10-20T17:01:40.2971113Z   Failed to restore /tmp/package-dependency-resolution_SJq1wJ/Project.csproj (in 6.1 sec).
2024-10-20T17:01:40.2971944Z 
2024-10-20T17:01:40.2972182Z Build FAILED.
:
:
2024-10-20T17:01:40.8703040Z updater | 2024/10/20 17:01:40 INFO <job_903580535> Updating Microsoft.ServiceFabric.Services.Remoting from 7.1.2338 to 7.1.2448
2024-10-20T17:01:40.8705310Z updater | running NuGet updater:
2024-10-20T17:01:40.8708251Z /opt/nuget/NuGetUpdater/NuGetUpdater.Cli update --repo-root /home/dependabot/dependabot-updater/repo --solution-or-project /home/dependabot/dependabot-updater/repo/test.csproj --dependency Microsoft.ServiceFabric.Services.Remoting --new-version 7.1.2448 --previous-version 7.1.2338 --result-output-path /tmp/update-result.json
:
:
2024-10-20T17:01:48.5181465Z   No global.json file found.
2024-10-20T17:01:48.5182399Z Running for project file [test.csproj]
2024-10-20T17:01:48.5183605Z Updating project [/home/dependabot/dependabot-updater/repo/test.csproj]
2024-10-20T17:01:48.5184862Z   Running 'PackageReference' project direct XML update
2024-10-20T17:01:48.5186585Z dotnet build in GetAllPackageDependenciesAsync failed. STDOUT:   Determining projects to restore...
2024-10-20T17:01:48.5189765Z /tmp/package-dependency-resolution_ngcwxu/Project.csproj : warning NU1900: Error occurred while getting package vulnerability data: Unable to load the service index for source https://pkgs.dev.azure.com/visiontrack/_packaging/visiontrack/nuget/v3/index.json.
2024-10-20T17:01:48.5194063Z /tmp/package-dependency-resolution_ngcwxu/Project.csproj : error NU1107: Version conflict detected for Microsoft.ServiceFabric.Services. Install/reference Microsoft.ServiceFabric.Services 7.1.2448 directly to project Project to resolve this issue. 
2024-10-20T17:01:48.5198481Z /tmp/package-dependency-resolution_ngcwxu/Project.csproj : error NU1107:  Project -> Microsoft.ServiceFabric.AspNetCore.Kestrel 7.1.2448 -> Microsoft.ServiceFabric.AspNetCore.Abstractions 7.1.2448 -> Microsoft.ServiceFabric.Services (= 7.1.2448) 
2024-10-20T17:01:48.5202101Z /tmp/package-dependency-resolution_ngcwxu/Project.csproj : error NU1107:  Project -> Microsoft.ServiceFabric.Services.Remoting 7.1.2338 -> Microsoft.ServiceFabric.Services (= 7.1.2338).
2024-10-20T17:01:48.5205032Z   Failed to restore /tmp/package-dependency-resolution_ngcwxu/Project.csproj (in 6.06 sec).
2024-10-20T17:01:48.5205989Z 
2024-10-20T17:01:48.5206202Z Build FAILED.
```

yikes ! ;-)