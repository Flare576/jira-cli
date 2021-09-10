# jira-cli

Custom commands and tools for using cloud or hosted Jira instances with [go-jira](https://github.com/go-jira/jira).


## Installation

```
  brew install flare576/scripts/jira-cli
  jira-setup # read the next part first
```

You should have on-hand:
- Email address in Jira
- Jira base URL
- Jira API token (see https://id.atlassian.com/manage/api-tokens)
  - If your project uses Hosted without the [API Tokens For
      Jira](https://marketplace.atlassian.com/apps/1221182/api-tokens-for-jira?hosting=server&tab=overview&utm_source=c),
      you may still be able to use [Jira Cookies](#jira-cookies)
- Main project name
- your "Shortname" (the name you use when you type [~first.last]) or otherwise tag yourself in Jira

The script will ask you for your information and write it to
`~/.jira.d/.doNotCommit.jira`

## Usage

If you run `jira -h`, you'll see the list of default commands (`help` through `session`), and then the ones I added.

| Command | Params | Result |
|---------|--------|--------|
| jira w[orkon] | TicketID | Set global story/ticket for `jira` commands |
| jira git | Branch Name | Create a new branch with JIRA_PREFIX/JIRA_ISSUE-BRANCH |
| jira s[print] | None | see the current sprint for your PROJECT |
| jira mine | None | see a list of unresolved tickets in PROJECT with you as ASSIGNEE |
| jira chrome | TicketID\* | Open ticket in Chrome |
| jira link | None | copies the link to the global ticket to the Mac clipboard |
| jira cookie | string | Hosted instances sometimes don't provide tokens; use sessions instead with daily cookies |
| jira i | None | Inspect current global story/ticket for `jira` commands |
| jira v | TicketID\* | View ticket details in `bat` if available, or `cat` otherwise |
| jira e | TicketID\* | Edit(vi) |
| jira c | TicketID\*, -m | Comment(vi) on ticket, follows `-m` pattern for predefined comment |
| jira t | State, TicketID\* | Transition ticket to new state (see `jira transitions`) |
| jira d | TicketID\* | Done: Transition ticket to "Ready for QA" (feel free to modify this to be your "Dev Done" state) |
| jira g | TicketID\* | Grab: Transition ticket to "In Progress" and assigns to you (feel free to modify this to your "In Progress" state) |
| jira qa | TicketID\* | QA: Transition ticket to "Testing" and sets you as the Reviewer (feel free to modify this to your "QA" state) |
| jira r | [State], TicketID\* | Review ticket by Comment(vi) on ticket, Transition to provided state or "Signoff" by default (feel free to modify this to your preferred Post-QA stateand with your preferred review template) |

> \*NOTE: TicketIDs are generally in the format PROJECT-123, and if you don't provide a TicketID the global story/ticket set by `jira w[orkon]` is used

### Hosted Instances

Sometimes you'll need to work with Jira instances that don't support Personal Access Tokens. First,
ask if they can:

- [Atlassian Access](https://www.atlassian.com/software/access)
- [API Tokens For Jira](https://marketplace.atlassian.com/apps/1221182/api-tokens-for-jira?hosting=server&tab=overview&utm_source=c),

If they can't/won't, you usually have two options

### Password login

The config provided here has `pasword-source: keyring` set. This means that after you provide your
password the first time, it will be saved in your OS's keyring, and then re-used when your cookie
expires. This is the next-best-thing to having a unique API key.

### Jira Cookies

**WARNING**

For hosted Jira instances, the cookies are, by design, short lived. You'll need to refresh them
frequently. 

Don't use this approach unless you absolutely have to.

Tokens are a secure, one-time-setup way of handling auth, and keyring passwords are a close
second... but sometimes stuff just doesn't work and you gotta do things "manually".

First, you'll need to get the cookie. Do this by:

1. Logging into your Jira instance in a browser.
1. Open Developer Tools
1. Open any story/task/etc.
1. Go to the Network tab of Developer Tools
1. Find a call to "resources" and click it
1. Scroll down to **REQUEST Headers** and find "Cookie"
1. Copy the value (it may have several keys: you're gonna need all of them)
1. Pop back over to the command line and type `jira cookie "<paste the cookie>"` - You'll need the
   quotes.
    ```
        # If there are other values in the cookie, don't worry; The script's goal is to allow `go-jira` to simulate a browser call, so leave them as-is.
        jira cookie "Cookie: _ga=GA1.2.GA1.2.xxx.xxx; AWSALB=xxxxxxxx/xxxxxxx/xxxxxx/xxxxxx+xxxxxxx; AWSALBCORS=xxxxxxx/xxxxxxx/xxxxxxx/xxxxxxx+xxxxxxx; atlassian.xsrf.token=xxxx-xxxx-xxxx-xxxx; JSESSIONID=xxxx"
    ```
1. Try `jira sprint` and see if you get data. If you do, don't celebrate yet - you'll probably need to
do this every day: the cookies expire, sometimes in hours. I know. Have a 🥃.

## Example

My day generally goes like this (assuming I'm on Project **ABC** with various ticket numbers)

```
jira mine # See what I've been assigned
jira s -w # s[print], Otherwise, I'll see what we have in the sprint I can snag
jira v ABC-1234 # v(iew), Look at a ticket
jira w ABC-1234 # w[orkon], Set the global issue, depends on ~/.jira.d existing
jira g # g(rab) If it wasn't mine already, grab it
jira v # v(iew), Look at it again, notice no more typing ABC-1234!
jira c -m 'This is an awesome ticket' # c(omment), Drop a comment on it
jira ts # (TransitionS), After I'm done, check where it can go next

# if I don't have  a shortcut like `d(one)` or `p(R Review)`, I use the longer syntax
jira t -s "Whatever State" -m "I've done what I can!"```

# In case I need to drop a link to a poor, non-CLI coworker - this will put it on the Mac clipboard
jira link
```

The `r(eviewed)` command is a good example of combining several actions together if you want to add
your own!

The last thing I want to mention is that all of the views you see are 100% configurable; see the
`.jira.d/templates` folder.
