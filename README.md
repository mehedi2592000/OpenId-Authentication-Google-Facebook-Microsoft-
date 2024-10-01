# OpenId-Authentication-Google-Facebook-Microsoft- 

# Start from Google Authentication
Befor start the google authentication we need to create google Outh2 clien Credentials. So lets start 
1. firt go to the [googlecredential_Link](https://console.cloud.google.com/)
2. after create the project and then going to the  apis & service option 
3. go to the "Credentials" and create credentials(oauth crediential)
4. when you create the crediential make sure "Authorized redirect URIs" option give the valid url in you project 
5. then download your credientials.

now create the google authetication in .net core 
1. first download Package in you project is "Authentication.google"
2. change you program.cs file 
   '''
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
   '''
3. change appsetting.json file 
    '''
      "Authentication": {
        "Google": {
            "ClientId": "29506814573-odjc0s7dc4gkdaqrab2utubjsqsdhd70.apps.googleusercontent.com",
            "ClientSecret": "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"
        }

        }
    '''
4. Add controller 
   '''
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
        var claims = result.Principal.Identities.FirstOrDefault().Claims.Select(claims => new
        {
            claims.Issuer,
            claims.OriginalIssuer,
            claims.Type,
            claims.Value
        });

        return Json(claims);
    }
   '''

Now you can run use google  authetication 
