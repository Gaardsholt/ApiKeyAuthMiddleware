# ApiKeyAuthMiddleware

# How to use

To enable the middleware you need to modify the `Startup.cs` and add `services.AddApiKeyAuthentication()` to the `ConfigureServices` method.
<br>
You need to provide some config to it, we only care about two properties "KeyName" and "ApiKeys".

- `KeyName` is which key to look for in either Query string or Header.
- `ApiKeys` is your super secret ApiKeys.

We DO NOT recommend hardcoding your ApiKeys, instead fetch them from somewhere else for example from an environment variable.
```csharp
Environment.GetEnvironmentVariable("API_KEYS").Split(',');
```


```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApiKeyAuthentication(a =>
    {
        a.KeyName = "ApiKey";
        a.ApiKeys = ["ApiKey-1", "ApiKey"]
    });
    // ...
}

// ...
 
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseAuthentication();
    app.UseAuthorization();

    // ...
}
```

# Protect an Action 

To protect an endpoint with the an ApiKey, you just add `[Authorize]` to whatever you want to protect.

```csharp
[Route("api/[controller]")]
[ApiController]
public class ValuesController : ControllerBase
{
    // GET: api/<ValuesController>
    [HttpGet]
    public IEnumerable<string> Get()
    {
        return new string[] { "value1", "value2" };
    }

    // GET api/<ValuesController>/5
    [Authorize]
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "value";
    }
}
```
Now you should be able to access `api/<ValuesController>` without an ApiKey and get an 401 error if you try the same on `api/<ValuesController>/5`.


# Protect the Controller 

If you want to protect the whole controller you just add the `[Authorize]` to the Controller instead of the action:
```csharp
[Authorize]
[Route("api/[controller]")]
[ApiController]
public class ValuesController : ControllerBase
{
    // GET: api/<ValuesController>
    [HttpGet]
    public IEnumerable<string> Get()
    {
        return new string[] { "value1", "value2" };
    }

    // GET api/<ValuesController>/5
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "value";
    }
}
```
