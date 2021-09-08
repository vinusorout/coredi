# coredi
.Net Core DI and others

* .csproj >> <ItemGroup> will have refrences to all the packages added by Nuget.
* Program.cs >> has main method
  * Main methods calls CreateHostBuilder(args).Build().Run();
  * This CreateHostBuilder setup the Host with the Starup class.

* Startup.cs, two main methods ConfigureServices and Configure
  * ConfigiureServices >> Used to configure the services like logging, and main custom functionality services, if you are using MVC, then By default you will find services.AddControllersWithViews(); to add MVC main service
    * services.AddDbContext() >> used to add EF and the DB conection strings to the EntityFramework
  * Configure >> Use this method to Configure the **HTTP request PIPELINE**.
    * Accept two parameters IApplicationBuilder and IWebEnviornment
    * IApplicationBuilder is used to configure the **HTTP request PIPELINE** by adding different **MIDDLEWARES** like app.UseHttpsRedirection(), app.UseStaticFiles(), app.UseRouting(), app.UseAuthorization(), and final middle ware should be app.UseEndpoints() where we define our routings.

![image](https://user-images.githubusercontent.com/27411868/132390505-c934f0ed-0ef6-4005-b214-59b492db13c2.png)

* appsetting.json >> for setting some predefined values like logging informations etc

* wwwroot (only when MVC) this folder will have all the static files like js, images, css etc
* regular MVC folders (Controllers, Models, Views)

* launchSettings.json >> will have the enviornmentVariables which we can use during the development or in actual enviornments.
 
* _ViewImports.cshtml >> this partial view provide a mechanism to make directives available to razor page globally, so that we dont have to import them to the pages indevidually. by default is adds all the **TAG HELPERS** using code "@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers" tage helper example "asp-area", "asp-controller", "asp-action", asp-route-<parametername>(for getting the cuuerent parametername and passing to next action) for navigation, "asp-for" while creating label or input for a Model property, asp-validation-summary="All/ModelOnly/None", asp-validation-for etc, it looks like HTML binding but it does it with the C# code, Use this same file to add new **CUSTOM** tag helper.
 
* _ViewStart.cshtml >> Use to set the values that applies to all the views like Layout = "_Layout".
 * You can also add the _ViewStart in individual view folder then this added file will be applied instead of root level.

 
## DI

 * The built-in container is represented by **IServiceProvider**
 * Two types of services Framework Services and Application Services

## Framework Services
 * IWebHostEnviornment, Lifetime: Singleton, Uses: Enviornment, hosting path etc
 * Microsoft.Extensions.Logging.ILogger<T>, Lifetime: Singleton, Uses: Logging
 * Microsoft.Extensions.Logging.ILoggerFactory, Lifetime: Singleton, Uses: Logging
 * Microsoft.AspNetCore.Http.IHttpContextFactory, Lifetime: Transient, Uses: Context with request/response
 * Microsoft.Extensions.Options.IOptions<TOptions>, Lifetime: Singleton, Uses: AppSettings
 * Microsoft.AspNetCore.Hosting.IStartup, Lifetime: Singleton, Uses: Startup app configurations
 * Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory, Lifetime: Transient, Uses: Appbuilder

### IOptions get key data from appsettings.json file
 * Add a new class and properties with same name as in appsettings.json file, for each sections
 * Now in ConfigureService

```c#
services.Configure<ClassCreatedForASectionInAppSettingJSON>(Configuration.GetSection("SectionNameWhosePropertiesYouDefinedInClass"));
```
 * Now use this in main code

```c#
private ClassNameCreatedForSection _options

ctor of main class (IOPtions<ClassNameCreatedForSection> options)
 {
    _options = options.Value;
 }
```

* Instead of doing this for each file, which will make the startup file lenghty, create a new folder in application say DI_Config, and register that in their
* For this create an EXTENSION method for the services which will return IServiceCollection object. which will also accept the IConfiguration  object to access appsetting.json values

```c#
 public static class DI_AppSettingsConfig
 {
    public static IServiceCollection AddAppSettingsConfig(this IServiceCollection services,IConfiguration configs)
    {
       services.Configure<ClassCreatedForASectionInAppSettingJSON>(configs.GetSection("SectionNameWhosePropertiesYouDefinedInClass"));
       return services;
    {
 }
 
 // NOW IN StartUp.cs file >> ConfigureService class
 services.AddAppSettingsConfig(Configuration)
```

## Services Lifetime

### SingleTon
* Same instance for the life of application (unless restarted)
* services.AddSingleton<>
 
### Scoped
* Same instance for one scope (one requesr in most cases)
* services.AddScoped<>
* Not ideal for multithreading
* Entity Framework context is an example of scoped lifetime
 
### Transient
* Different instance everytime the service is injected.
* services.AddTransient<> 

### Custom Middleware
```c# 
 public class CustomMiddleware
 {
     private readonly RequestDelegate _next;

     public CustomMiddleware(RequestDelegate next)
     {
         _next = next;
     }

     public async Task InvokeAsync(HttpContext context,TransientService transientService,
         ScopedService scopedService, SingletonService singletonService)
     {
         context.Items.Add("CustomMiddlewareTransient", "Transient Middleware - " + transientService.GetGuid());
         context.Items.Add("CustomMiddlewareScoped", "Scoped Middleware - " + scopedService.GetGuid());
         context.Items.Add("CustomMiddlewareSingleton", "Singleton Middleware - " + singletonService.GetGuid());

         await _next(context);
     }
 }
```
Now use this in startup.cs confifure

```c#
app.UseMiddleware<CustomMiddleware>();
```
 
 
## Different Injections

### Controllers
* in ctor of controllers
 
### Action
* In the action methods, using the [FromServices]

```c#
 public IActionResult AllConfigSettings(
     [FromServices] IOptions<StripeSettings> stripeOptions,
     [FromServices] IOptions<SendGridSettings> sendGridOptions
     )
 {
 use stripeOptions.Value
 .
 .
 }
```
 
### View
* direct in view

```c#
@using Microsoft.Extensions.Options
@inject IOptions<WazeCredit.Utility.AppSettingsClasses.WazeForecastSettings> wazeForecastSettings
 
 now use @wazeForecastSettings.Value
 
```
 
### Middleware
* like we did in our cutom middleware 
* NOTE: if we get the service inside the ctor of middleware then it will keep the same instance without considering the lifecycle hook of service, because the middleware ctor runs only at the application startup
 
 
## Advance

### TryAddTransient
 * Try add will ensure that no duplication is registered for same Interface
 * Let say we register ServiceV1 to IService
 * Now if we try to add Servicev2 to IService, and we use AddTransient<>, then it will not give error and **the last one will be injected**
 * But if we use the TraAdd then it will not register the new ServiceV2, but wont give any error too

### Replace
* When you want to replace the previously injected service
 
```c#
 services.AddTransient(IService, ServiceV1>();
 services.Replace(ServiceDescriptor.Transient<IService, ServiceV2>());
```
 
### RemoveAll
* To remove the injection

```c#
 services.RemoveAll<IService>();
```
 
 
 
 
 
 
 
 
 
