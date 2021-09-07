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

 

  
  
  
