# OpenId-Authentication-Google-Facebook-Microsoft- 

# OpenId Authentication (Google, Facebook, Microsoft)

## Start with Google Authentication

Before starting the Google authentication setup, we need to create Google OAuth2 client credentials. Let's go step by step:

1. First, go to the [Google Credentials Link](https://console.cloud.google.com/).
2. Create a project, then navigate to the **APIs & Services** option.
3. Go to **Credentials** and create credentials (OAuth credential).
4. When creating the credential, make sure to provide a valid URL in your project's **Authorized Redirect URIs** option.
5. Download your credentials.

### Now, set up Google authentication in .NET Core:

1. First, download the package for Google authentication in your project: `Microsoft.AspNetCore.Authentication.Google`.
2. Modify your `Program.cs` file as follows:

    ```csharp
    builder.Services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = GoogleDefaults.AuthenticationScheme; // Set default scheme if needed
    })
    .AddCookie()
    .AddGoogle(googleOptions =>
    {
        googleOptions.ClientId = builder.Configuration["Authentication:Google:ClientId"];
        googleOptions.ClientSecret = builder.Configuration["Authentication:Google:ClientSecret"];
    });
    ```

3. Update the `appsettings.json` file:

    ```json
    {
      "Authentication": {
        "Google": {
          "ClientId": "29506814573-odjc0s7dc4gkdaqrab2utubjsqsdhd70.apps.googleusercontent.com",
          "ClientSecret": "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"
        }
      }
    }
    ```

4. Add the following controller to handle Google authentication:

    ```csharp
    public async Task Login()
    {
        await HttpContext.ChallengeAsync(GoogleDefaults.AuthenticationScheme,
            new AuthenticationProperties
            {
                RedirectUri = Url.Action("GoogleResponse")
            });
    }

    public async Task<IActionResult> GoogleResponse()
    {
        var result = await HttpContext.AuthenticateAsync(CookieAuthenticationDefaults.AuthenticationScheme);
        var claims = result.Principal.Identities.FirstOrDefault().Claims.Select(claim => new
        {
            claim.Issuer,
            claim.OriginalIssuer,
            claim.Type,
            claim.Value
        });

        return Json(claims);
    }
    ```

Now, you should be able to run and use Google authentication in your .NET Core project.

