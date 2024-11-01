
# Lab 1: Use the Scientific Method to Solve a Login Issue

## Objective

- You should be able to troubleshoot a login issue by using the scientific method.

### Problem

"Over the weekend, colleagues ran user account maintenance. This morning, the bob user reported that their SSH login no longer works on node1. The user states that their password is `redhat`, and that passwordless SSH normally works fine from his workstation."

- You verify that the user's expected settings are a home directory at `/home/bob`, the UID and GID should both be `1000`, and that this user's preferred shell is `bash`.

(This exercise is intentionally simple, and is designed for you to practice including the scientific method in your troubleshooting workflow.)

### Step 1: Reproduce the Problem and Collect Relevant Information

1.1. From a command prompt on workstation, attempt to use the SSH command to log in to the bob account on node1.

What do you notice, and what can be hypothesized from this information?

```bash
[bob@workstation ~]$ ssh bob@node1
Activate the web console with: systemctl enable --now cockpit.socket
This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
To register this system, run: insights-client --register
Connection to server closed.
[bob@workstation ~]$
```

*"The messages indicate that authentication succeeded, but the connection was closed immediately after."*

1.2. Open a console window to node1 and attempt to log in as `bob` with the password `redhat`. What happens?

*"The login proceeds without authentication error messages, but the session is closed immediately."*

### Step 2: Gather More Information About the Target Server

2.1. Attempt to use the SSH command to log in to the root account on node1. If required and unmodified, the password is `redhat`.

```bash
[bob@workstation ~]$ ssh root@node1
...output omitted...
Last login: Wed Sep 1 17:06:02 2021 from 172.25.250.9
[root@node1 ~]# whoami
root
```

*"Logging in to the root account succeeds."*

2.2. As the root user on node1, attempt to use the `su -` command to switch to the bob account.

```bash
[root@node1 ~]# su - bob
Last login: Wed Sep 1 17:07:27 EDT 2021 from 172.25.250.9 on pts/0
[root@node1 ~]# whoami
root
```

*"Switching to the bob account did not succeed."*

2.3. Because the root account works, use it to gather information about the bob account. Verify when the bob last successfully logged in.

```bash
[root@node1 ~]# lastlog -u bob
Username Port From Latest
bob pts/0 Wed Sep 1 17:07:27 -0400 2021
```

*"This output verifies that the bob user successfully logged in during previous tests, suggesting that something is causing the session to end prematurely."*

2.4. As root on node1, use the `getent` command to view the bob user's settings.

```bash
[root@node1 ~]# getent passwd bob
bob:x:1000:1000:Student User:/home/bob:/bin/false
```

*"The bob account is configured with the **`/bin/false`** shell, which restricts an account to non-interactive use only."*

### Step 3: Create a Problem Statement

Although the solution appears obvious, creating a problem statement is a proven technique to verify that you can accurately define the problem. If you researched that other users do not have the same problem, and that the problem does not occur for `bob` on other servers, then here is an example of an accurate problem statement:

*"Since the previous weekend, the bob user can access node1 but is unable to create a working shell session. Because other users can start sessions on node1, and the bob user can start sessions on other servers, the problem appears only with the bob use of node1."*

### Step 4: Formulate Hypotheses

With the information gathered, form one or more hypotheses of what might be wrong with the bob account.

3.1. Looking at the `getent` output, note that the user's shell is set to `/bin/false`.

*"This shell is not valid for interactive access and is not the user's preferred shell."*

3.2. Formulate a test for your hypothesis.

*"If the shell is the problem, then test by changing the user configuration to the user's preferred shell and attempting the login again."*

### Step 5: Reset the Shell and Verify the Problem Resolution

4.1. Reset the shell for bob on node1 to `/bin/bash`.

```bash
[root@node1 ~]# chsh -s /bin/bash bob
Changing shell for bob.
Shell changed.
```

4.2. Attempt to use the SSH command from workstation to log in to the bob account on node1.

```bash
[bob@workstation ~]$ ssh bob@node1
...output omitted...
[bob@node1 ~]$
```

*"Login to a working shell is successful."*

### Step 6: Verify All Test Results and Implement the Solution

In this example, the only hypothesis proved to be the correct cause of the problem. In scenarios where there are more hypotheses, test each one and record the results.

*"Test: Modified the user account to use **`/bin/bash`** in the node1 local user configuration and logged in again. Result: success."*

### Step 7: Final Solution

After recording all test results, the final step is to choose and implement the best solution. During your testing, you verified on one test that the bob user can access and create a shell on node1.

The problem is resolved when the inverse of the original problem statement is true. For this scenario, this is an example of the inverse problem statement as the solution:

*"Currently, the bob user can successfully access node1 and create a working shell session."*

### Step 8: Return to Workstation

Return to workstation as the bob user.

```bash
[bob@node1 ~]$ exit
[bob@workstation ~]$
```

* Done!
