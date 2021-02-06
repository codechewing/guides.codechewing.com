# Trigger an automatic shutdown from command prompt on Windows

You can shutdown your Windows machine from the command prompt – and also delay the
shutdown time for say, 5 minutes, from when you execute the command.

I’d say this feature has two main uses:

1. You want to schedule your computer to shutdown after a certain amount of time.
Maybe it’s finishing a task, and needs an extra 10 minutes before you shutdown? But you have to leave NOW.
2. Or, to play a cruel prank on your friend. (use at your own peril – I cannot be held responsible!)

You can also cancel Windows from shutting down when triggered from the command prompt,
which you’ll see how to do below – so please feel free to try these as you go along,
and just cancel them at any time.

Firstly, open your windows command terminal. Keyboard shortcut: <kbd>Windows button</kbd> + <kbd>R</kbd>.
Then type in <kbd>cmd</kbd>, and press <kbd>Enter</kbd>:

<img class="img-thumbnail" src="/assets/images/posts/shutdown-windows-command-prompt/run-cmd-prompt-shortcut.jpg"
alt="run command prompt shortcut">

Bringing us here:

<img class="img-thumbnail" alt="command window"
src="/assets/images/posts/shutdown-windows-command-prompt/cmd-window.jpg">

Now, to do the standard shutdown, which will perform the shutdown in 60 seconds, we type <kbd>shutdown -s</kbd>:

<img class="img-thumbnail" alt="default shutdown cmd line"
src="/assets/images/posts/shutdown-windows-command-prompt/default-shutdown-cmd-line.jpg">

Press <kbd>Enter</kbd>, and you'll be notified of the automatic shutdown in 60 seconds!
The alert notification will appear:

<img class="img-thumbnail" alt="automatic shutdown notice from cmd prompt"
src="/assets/images/posts/shutdown-windows-command-prompt/automatic-shutdown-notice-from-cmd-prompt.jpg">

Don't worry, you can **cancel your computer from shutting down**,
by typing <kbd>shutdown /a</kbd> into the command prompt:

<img class="img-thumbnail" alt="cancel windows shutdown cmd line"
src="/assets/images/posts/shutdown-windows-command-prompt/cancel-windows-shutdown-cmd-line.jpg">

You will be notified of the cancellation. With something like the following:

<img class="img-thumbnail" alt="windows shutdown cancel"
src="/assets/images/posts/shutdown-windows-command-prompt/windows-shutdown-cancel.jpg">

You can also cancel the shutdown in the start menu, by again typing <kbd>shutdown /a</kbd>. Like so:

<img class="img-thumbnail" alt="stop windows shutdown"
src="/assets/images/posts/shutdown-windows-command-prompt/stop-windows-shutdown.jpg">

## Schedule a time to wait before shutting down using command prompt

You can add another option to the shutdown call inside of command prompt to provide a time delay before shutting
down the computer. To do so, you need to type <kbd>shutdown -s -t 120</kbd>.
That will tell Windows to wait 120 seconds before shutting down:

<img class="img-thumbnail" alt="schedule shutdown windows command"
src="/assets/images/posts/shutdown-windows-command-prompt/schedule-shutdown-windows-command.jpg">

You'll receive a similar looking notice as below:

<img class="img-thumbnail" alt="scheduled shutdown notice"
src="/assets/images/posts/shutdown-windows-command-prompt/scheduled-shutdown-notice.jpg">

I will of course cancel this command with <kbd>shutdown /a</kbd> afterwards!

### How to add a custom message to the notice box on shutdown with command prompt

And finally, here's a technique to add a custom message to the notification box.
Maybe "HAHAHA LMAO!!1!" if it's a prank on your unlucky friend!?

For the above example, you would type <kbd>shutdown -s -t 60 -c "HAHAHA LMAO!!1!"</kbd>. Like so:

<img class="img-thumbnail" alt="add message to shutdown with command prompt"
src="/assets/images/posts/shutdown-windows-command-prompt/add-message-to-shutdown-with-command-prompt.jpg">

And after pressing enter, you'll get an evil notice message:

<img class="img-thumbnail" alt="custom shutdown message windows"
src="/assets/images/posts/shutdown-windows-command-prompt/custom-shutdown-message-windows.jpg">

### Farewell

With great power, comes great responsibility. Remember to save your work and enjoy the power of shutting down Windows
directly from the command prompt.
