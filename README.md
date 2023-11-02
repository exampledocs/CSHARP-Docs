# SkyAuth-CSHARP-Example
SkyAuth C# example for the https://skyproject.cc authentication system.

## **Bugs**

If the default example not added to your software isn't functioning how it should, please report a bug here https://skyproject.cc/app/?page=support

However, we do **NOT** provide support for adding SkyAuth to your project. If you can't figure this out you should use Google or YouTube to learn more about the programming language you want to sell a program in.

## `SkyAuthApp` instance definition

Visit https://skyproject.cc/app/ and select your application, then click on the **C#** tab

It'll provide you with the code which you should replace with in the `Program.cs` file (or `Login.cs` file if using Form example)

```cs
public static api SkyAuthApp = new api(
    name: "example",
    ownerid: "JjPMBVlIOf",
    secret: "fb40d586f4b189e04e5c18c3c94b7e72221be3f6551995adc05236948d1762bc",
    version: "1.0"
);
```

## Initialize application

You must call this function prior to using any other SkyAuth function. Otherwise the other SkyAuth function won't work.

```cs
SkyAuthApp.init();
if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
```

## Display application information

```cs
SkyAuthApp.fetchStats();
Console.WriteLine("\n Application Version: " + SkyAuthApp.app_data.version);
Console.WriteLine(" Customer panel link: " + SkyAuthApp.app_data.customerPanelLink);
Console.WriteLine(" Number of users: " + SkyAuthApp.app_data.numUsers);
Console.WriteLine(" Number of online users: " + SkyAuthApp.app_data.numOnlineUsers);
Console.WriteLine(" Number of keys: " + SkyAuthApp.app_data.numKeys);
```

## Check session validation

Use this to see if the user is logged in or not.

```cs
SkyAuthApp.check();
Console.WriteLine($" Current Session Validation Status: {SkyAuthApp.response.message}");
```

## Check blacklist status

Check if HWID or IP Address is blacklisted. You can add this if you want, just to make sure nobody can open your program for less than a second if they're blacklisted. Though, if you don't mind a blacklisted user having the program for a few seconds until they try to login and register, and you care about having the quickest program for your users, you shouldn't use this function then. If a blacklisted user tries to login/register, the SkyAuth server will check if they're blacklisted and deny entry if so. So the check blacklist function is just auxiliary function that's optional.

```cs
if(SkyAuthApp.checkblack()) {
    Environment.Exit(0);  // terminate program if user blacklisted.
}
```

## Login with username/password

```cs
string username;
string password;
Console.WriteLine("\n\n Enter username: ");
username = Console.ReadLine();
Console.WriteLine("\n\n Enter password: ");
password = Console.ReadLine();
SkyAuthApp.login(username, password);
if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
```

## Register with username/password/key

```cs
string username, password, key, email;
Console.Write("\n\n Enter username: ");
username = Console.ReadLine();
Console.Write("\n\n Enter password: ");
password = Console.ReadLine();
Console.Write("\n\n Enter license: ");
key = Console.ReadLine();
Console.Write("\n\n Enter email (just press enter if none): ");
email = Console.ReadLine();
SkyAuthApp.register(username, password, key, email);
if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
```

## Upgrade user username/key

```cs
string username;
string password;
string key;
Console.WriteLine("\n\n Enter username: ");
username = Console.ReadLine();
Console.WriteLine("\n\n Enter license: ");
key = Console.ReadLine();
SkyAuthApp.upgrade(username, key);
// don't proceed to app, user hasn't authenticated yet.
Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
Thread.Sleep(2500);
Environment.Exit(0);
```

## Login with just license key

Users can use this function if their license key has never been used before, and if it has been used before. So if you plan to just allow users to use keys, you can remove the login and register functions from your code.

```cs
string key;
Console.WriteLine("\n\n Enter license: ");
key = Console.ReadLine();
SkyAuthApp.license(key);
if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
```

##  Login with web loader

```cs
SkyAuthApp.web_login();

Console.WriteLine("\n Waiting for button to be clicked");
SkyAuthApp.button("close");
```

## Forgot password

Allow users to enter their account information and recieve an email to reset their password.

```cs
string username, email;
Console.Write("\n\n Enter username: ");
username = Console.ReadLine();
Console.Write("\n\n Enter email: ");
email = Console.ReadLine();
SkyAuthApp.forgot(username, email);
// don't proceed to app, user hasn't authenticated yet.
Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
Thread.Sleep(2500);
Environment.Exit(0);
```

## User Data

Show information for current logged-in user.

```cs
Console.WriteLine("\n User data:");
Console.WriteLine(" Username: " + SkyAuthApp.user_data.username);
Console.WriteLine(" IP address: " + SkyAuthApp.user_data.ip);
Console.WriteLine(" Hardware-Id: " + SkyAuthApp.user_data.hwid);
Console.WriteLine(" Created at: " + UnixTimeToDateTime(long.Parse(SkyAuthApp.user_data.createdate)));
if (!String.IsNullOrEmpty(SkyAuthApp.user_data.lastlogin)) // don't show last login on register since there is no last login at that point
    Console.WriteLine(" Last login at: " + UnixTimeToDateTime(long.Parse(SkyAuthApp.user_data.lastlogin)));
Console.WriteLine(" Your subscription(s):");
for (var i = 0; i < SkyAuthApp.user_data.subscriptions.Count; i++)
{
    Console.WriteLine(" Subscription name: " + SkyAuthApp.user_data.subscriptions[i].subscription + " - Expires at: " + UnixTimeToDateTime(long.Parse(SkyAuthApp.user_data.subscriptions[i].expiry)) + " - Time left in seconds: " + SkyAuthApp.user_data.subscriptions[i].timeleft);
}
```

## Check subscription name of user

If you want to wall off parts of your app to only certain users, you can have multiple subscriptions with different names. Then, when you create licenses that correspond to the level of that subscription, users who use those licenses will get a subscription with the name of the subscription that corresponds to the level of the license key they used. The `SubExist` function is in the `Program.cs` file

```cs
if (SubExist("default"))
{
    Console.WriteLine(" Default Subscription Exists");
}
// See if another sub exists with name 
if (SubExist("premium"))
{
    Console.WriteLine(" Premium Subscription Exists");
}
```

## Show list of online users

```cs
var onlineUsers = SkyAuthApp.fetchOnline();
if (onlineUsers != null)
{
    Console.Write("\n Online users: ");
    foreach (var user in onlineUsers)
    {
        Console.Write(user.credential + ", ");
    }
    Console.WriteLine("\n");
}
```

## Application variables

A string that is kept on the server-side of SkyAuth. On the dashboard you can choose for each variable to be authenticated (only logged in users can access), or not authenticated (any user can access before login). These are global and static for all users, unlike User Variables which will be dicussed below this section.

```cs
string appvar = SkyAuthApp.var("variableNameHere");
if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
else
    Console.WriteLine("\n App variable data: " + appvar);
```

## User Variables

User variables are strings kept on the server-side of SkyAuth. They are specific to users. They can be set on Dashboard in the Users tab, via SellerAPI, or via your loader using the code below. `discord` is the user variable name you fetch the user variable by. `test#0001` is the variable data you get when fetching the user variable.

```cs
SkyAuthApp.setvar("discord", "test#0001");
if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
else
    Console.WriteLine("\n Successfully set user variable");
```

And here's how you fetch the user variable:

```cs
string uservar = SkyAuthApp.getvar("discord");
if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
else
    Console.WriteLine("\n User variable value: " + uservar);
```

## Application Logs

Can be used to log data. Good for anti-debug alerts and maybe error debugging. If you set Discord webhook in the app settings of the Dashboard, it will send log messages to your Discord webhook rather than store them on site. It's recommended that you set Discord webhook, as logs on site are deleted 1 month after being sent.

You can use the log function before login & after login.

```cs
SkyAuthApp.log("hello I wanted to log this");
```

## Ban the user

Ban the user and blacklist their HWID and IP Address. Good function to call upon if you use anti-debug and have detected an intrusion attempt.

Function only works after login.

```cs
SkyAuthApp.ban();
```

## Ban the user (with reason)

Ban the user and blacklist their HWID and IP Address. Good function to call upon if you use anti-debug and have detected an intrusion attempt.

Function only works after login.

The reason paramater will be the ban reason displayed to the user if they try to login, and visible on the SkyAuth dashboard.

```cs
SkyAuthApp.ban("Don't try to crack my loader, cunt.");
```

## Server-sided webhooks

Send HTTP requests to URLs securely without leaking the URL in your application. You should definitely use if you want to send requests to SellerAPI from your application, otherwise if you don't use you'll be leaking your seller key to everyone. And then someone can mess up your application.

1st example is how to send request with no POST data. just a GET request to the URL. `7kR0UedlVI` is the webhook ID, `https://skyproject.cc/api/seller/?sellerkey=sellerkeyhere&type=black` is what you should put as the webhook endpoint on the dashboard. This is the part you don't want users to see. And then you have `&ip=1.1.1.1&hwid=abc` in your program code which will be added to the webhook endpoint on the SkyAuth server and then the request will be sent.

2nd example includes post data. it is form data. it is an example request to the SkyAuth API. `7kR0UedlVI` is the webhook ID, `https://skyproject.cc/api/1.2/` is the webhook endpoint.

3rd examples included post data though it's JSON. It's an example reques to Discord webhook `7kR0UedlVI` is the webhook ID, `https://discord.com/api/webhooks/...` is the webhook endpoint.

```cs
// example to send normal request with no POST data
string resp = SkyAuthApp.webhook("7kR0UedlVI", "&ip=1.1.1.1&hwid=abc");

// example to send form data
resp = SkyAuthApp.webhook("7kR0UedlVI", "", "type=init&name=test&ownerid=j9Gj0FTemM", "application/x-www-form-urlencoded");

// example to send JSON
resp = SkyAuthApp.webhook("7kR0UedlVI", "", "{\"content\": \"webhook message here\",\"embeds\": null}", "application/json"); // if Discord webhook message successful, response will be empty

if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
else
    Console.WriteLine("\n Response received from webhook request: " + resp);
```

## Download file

`385625` is the file ID you get from the dashboard after adding file.

```cs
byte[] result = SkyAuthApp.download("385625");
if (!SkyAuthApp.response.success)
{
    Console.WriteLine("\n Status: " + SkyAuthApp.response.message);
    Thread.Sleep(2500);
    Environment.Exit(0);
}
else
    File.WriteAllBytes(Directory.GetCurrentDirectory() + "\\test.txt", result);
```

## Chat channels

Allow users to communicate amongst themselves in your program.

Example from the form example on how to fetch the chat messages.

```cs
var messages = Login.SkyAuthApp.chatget("chatChannelNameHere");
if (messages == null)
{
    dataGridView1.Rows.Insert(0, "SkyAuth", "No Chat Messages", UnixTimeToDateTime(DateTimeOffset.Now.ToUnixTimeSeconds()));
}
else
{
    foreach (var message in messages)
    {
        dataGridView1.Rows.Insert(0, message.author, message.message, UnixTimeToDateTime(long.Parse(message.timestamp)));
    }
}
```

Example from the form example on how to send chat message.

```cs
if (Login.SkyAuthApp.chatsend(chatmsg.Text, chatchannel))
{
    dataGridView1.Rows.Insert(0, Login.SkyAuthApp.user_data.username, chatmsg.Text, UnixTimeToDateTime(DateTimeOffset.Now.ToUnixTimeSeconds()));
}
else
    chatmsg.Text = "Status: " + Login.SkyAuthApp.response.message;
```
