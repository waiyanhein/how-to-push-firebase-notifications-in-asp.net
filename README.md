# how-to-push-firebase-notifications-in-asp.net

This is the simple of how to push notifications using Firebabse Cloud Messaging in ASP.NET


###Pushing notification or message to single device

```
public string PushNotifications(string token)//Token is the firebase device registration token
{
      using (var client = new WebClient())
      {
          client.Headers.Add(string.Format("Authorization:key={0}", FIREBASE_SERVER_KEY)); //get from firebase
          var values = new NameValueCollection();

          values["registration_id"] = token;
          values["data.message"] = "This is the test FCM test message";
          var response = client.UploadValues("https://fcm.googleapis.com/fcm/send", values);
          string responseString = Encoding.Default.GetString(response);
          if (!string.IsNullOrEmpty(responseString))
          {
            return responseString;
          }
          else
          {
             return "Something went wrong";
          }
      }
}
```

###Pushing to multiple devices using Firebase Topic Messaging
####1. Subscribe to a topic at the client(Android Device)
```
FirebaseMessaging.getInstance().subscribeToTopic("news");
```
####2. Push to the all the devices subscribed to topic in asp.net like this
```
public string PushNotifications()
{
    var result = "-1";
    var webAddr = "https://fcm.googleapis.com/fcm/send";

    var httpWebRequest = (HttpWebRequest)WebRequest.Create(webAddr);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Headers.Add("Authorization:key=" +FIREBASE_SERVER_KEY);
    httpWebRequest.Method = "POST";

    using (var streamWriter = new StreamWriter(httpWebRequest.GetRequestStream()))
    {
         string json = "{\"to\": \"/topics/news\",\"data\": {\"message\": \"This is a Firebase Cloud Messaging Topic Message!\",}}";


         streamWriter.Write(json);
         streamWriter.Flush();
    }

    var httpResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    using (var streamReader = new StreamReader(httpResponse.GetResponseStream()))
    {
         result = streamReader.ReadToEnd();
    }

    return result;
}
```
In the above client subscribed to "news" topic and so we push to "news" topic at server.
