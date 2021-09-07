# coredi
.Net Core DI and others

* .csproj >> <ItemGroup> will have refrences to all the packages added by Nuget.
* Program.cs >> has main method
  * Main methods calls CreateHostBuilder(args).Build().Run();
  * This CreateHostBuilder setup the Host with the Starup class.

* Startup.cs, two main methods ConfigureServices and Configure
  * ConfigiureServices >> Used to configure the services like logging, and main custom functionality services, if you are using MVC, then By default you will find services.AddControllersWithViews(); to add MVC main service
  * Configure >> Use this method to Configure the **HTTP request PIPELINE**.
    * Accept two parameters IApplicationBuilder and IWebEnviornment
    * IApplicationBuilder is used to configure the **HTTP request PIPELINE** by adding different **MIDDLEWARES** like app.UseHttpsRedirection(), app.UseStaticFiles(), app.UseRouting(), app.UseAuthorization(), and final middle ware should be app.UseEndpoints() where we define our routings.

![image](https://user-images.githubusercontent.com/27411868/132390505-c934f0ed-0ef6-4005-b214-59b492db13c2.png)

* appsetting.json >> for setting some predefined values like logging informations etc

* wwwroot (only when MVC) this folder will have all the static files like js, images, css etc
* regular MVC folders (Controllers, Models, Views)

* launchSettings.json >> will have the enviornmentVariables which we can use during the development or in actual enviornments.
  
  
  
