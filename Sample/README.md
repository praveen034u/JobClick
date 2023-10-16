# Quickstart Sample

This example shows how to add login/logout and extract user profile information from claims.

You can read a quickstart for this sample here.

## Requirements

- [.NET SDK](https://dotnet.microsoft.com/download) (.NET 6.0+)

## To run this project

1. Open the solution in Visual Studio and pick iOS, macOS, Android or Windows to run the application.

## Important Snippets

### Configuring Android

Create a new Activity that extends `Microsoft.Maui.Authentication.WebAuthenticatorCallbackActivity`:

```csharp
[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop, Exported = true)]
[IntentFilter(new[] { Intent.ActionView },
              Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
              DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticatorActivity : Microsoft.Maui.Authentication.WebAuthenticatorCallbackActivity
{
    const string CALLBACK_SCHEME = "myapp";
}
```

The above activity will ensure the application can handle the `myapp://callback` URL when Auth0 redirect back to the Android application after logging in.

### Configuring Windows

To support Windows, our SDK relies on WinUIEx, a community-built package to provide a WebAuthenticator that supports Windows.

To make sure it can properly reactivate your application after being redirected back go Auth0, you need to do two things:

- Add the `myapp` protocol to the `Package.appxmanifest`
  ```xml
  <Applications>
    <Application Id="App" Executable="$targetnametoken$.exe" EntryPoint="$targetentrypoint$">
      <Extensions>
        <uap:Extension Category="windows.protocol">
          <uap:Protocol Name="myapp"/>
        </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
  ```
- Call `WinUIEx.WebAuthenticator.CheckOAuthRedirectionActivation()` in the Windows specific App.xaml.cs file
  ```csharp
  public App()
  {
    if (WinUIEx.WebAuthenticator.CheckOAuthRedirectionActivation())
      return;
  
    this.InitializeComponent();
  }
  ```

### Create an instanstance of the Auth0Client

```csharp
Auth0Client client = new Auth0Client(new Auth0ClientOptions
{
    Domain = "",
    ClientId = "",
    Scope = "openid profile email"
});
```
### Login

```csharp
var extraParameters = new Dictionary<string, string>();
var audience = ""; // FILL WITH AUDIENCE AS NEEDED

if (!string.IsNullOrEmpty(audience))
    extraParameters.Add("audience", audience);

var result = await client.LoginAsync(extraParameters);
```

### 4. User Profile

```csharp
var result = await client.LoginAsync(extraParameters);
var user = result.User;
var name = user.Identity.Name;
```

### 5. Logout

```csharp
await client.LogoutAsync();
```
