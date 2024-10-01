# OpenId Authentication (Google, Facebook, Microsoft)

## Start with Google Authentication

Before starting the Google authentication setup, we need to create Google OAuth2 client credentials. Let's go step by step:

1. First, go to the [Google Credentials Link](https://console.cloud.google.com/).
2. Create a project, then navigate to the **APIs & Services** option.
3. Go to **Credentials** and create credentials (OAuth credential).
4. When creating the credential, make sure to provide a valid URL in your project's **Authorized Redirect URIs** option and must give the endpoint **signin-google** like -(https://localhost:7025/signin-google).
5. Download your credentials.

### Now, set up Google authentication in .NET Core:

1. First, download the package for Google authentication in your project: `Microsoft.AspNetCore.Authentication.Google`.

## Start with FaceBook Authentication

Before starting the FaceBook authentication setup, we need to create FaceBook OAuth2 client credentials. Let's go step by step:

1. First, go to the [FaceBook Credentials Link](https://developers.facebook.com/).
2. Create a project,Go to **Credentials** and create credentials (OAuth credential).
4. When creating the credential, make sure to provide a valid URL in your project's **Authorized Redirect URIs** option and must give the endpoint **signin-FaceBook** like -(https://localhost:7025/signin-facebook).
5. Download your credentials.
6. For more help you can see this vedio [Facebook credential make vedio](https://www.youtube.com/watch?v=LLlpH3vZVkg&ab_channel=CodingwithKazim)

### Now, set up FaceBook authentication in .NET Core:

1. First, download the package for Google authentication in your project: `Microsoft.AspNetCore.Authentication.FaceBook`.


# full code of facebook and google authentication
. Modify your `Program.cs` file as follows:

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

        })
        .AddFacebook(FaceBookOptions =>
        {
            FaceBookOptions.ClientId = builder.Configuration["Authentication:FaceBook:ClientId"];
            FaceBookOptions.ClientSecret = builder.Configuration["Authentication:FaceBook:ClientSecret"];

        });
    ```

3. Update the `appsettings.json` file:

    ```json
    "Authentication": {
        "Google": {
            "ClientId": "29506814573-odjc0s7dc4gkdaqrab2utubjsqsdhd70.apps.googleusercontent.com",
            "ClientSecret": "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"
        },
        "FaceBook": {
            "ClientId": "1263472334647144",
            "ClientSecret": "6957df53ea2248d3c42a117bc62d5a24"
        }

        }
    ```

4. Add the following controller to handle Google authentication:

    ```csharp
    public async Task Login()
        {
            await HttpContext.ChallengeAsync(FacebookDefaults.AuthenticationScheme,
                new AuthenticationProperties
                {
                    RedirectUri = Url.Action("FaceBookResponse")
                });
        }

        public async Task<IActionResult> FaceBookResponse()
        {
            var result = await HttpContext.AuthenticateAsync(CookieAuthenticationDefaults.AuthenticationScheme);

            // Extract the claims and create a consolidated JSON object
            var claims = result.Principal.Identities.FirstOrDefault().Claims.ToList();

            // Create a consolidated JSON object
            var userInfo = new
            {
                NameIdentifier = claims.FirstOrDefault(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier")?.Value,
                Email = claims.FirstOrDefault(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress")?.Value,
                Name = claims.FirstOrDefault(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name")?.Value,
                GivenName = claims.FirstOrDefault(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname")?.Value,
                Surname = claims.FirstOrDefault(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname")?.Value
            };

            return Json(userInfo);
        }
    ```

Now, you should be able to run and use Google authentication in your .NET Core project.

