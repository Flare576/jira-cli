# jiraCookie

Allows you to use hosted (vs. Cloud) Jira instances with [go-jira](https://github.com/go-jira/jira).

## WARNING

These cookies are, by design, short lived. You'll need to refresh them frequently, unlike Personal
Access Tokens that Atlassian generally recommends for Jira.

## Installation

```
  brew install flare576/scripts/jiraCookie
```

## Usage

To get a cookie for Jira to use, you'll need to visit your Jira system in your browser with Dev
tools open to the **Network** tab. Look for an XHR call (you can use the filters at the top of the panel) such as
`resources`, and then scroll to the Request Headers. Right-click the `Cookie` entry and choose
"Copy".

When you call the `jiraCookie` command, be sure to wrap the entire value in quotes:

```
jiraCookie "Cookie: _ga=GA1.2.GA1.2.xxx.xxx; AWSALB=xxxxxxxx/xxxxxxx/xxxxxx/xxxxxx+xxxxxxx; AWSALBCORS=xxxxxxx/xxxxxxx/xxxxxxx/xxxxxxx+xxxxxxx; atlassian.xsrf.token=xxxx-xxxx-xxxx-xxxx; JSESSIONID=xxxx"
```

If there are other values in the cookie, don't worry; The script's goal is to allow `go-jira` to
simulate a browser call, so leave them as-is.

