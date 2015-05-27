---
layout: default
title: Making A Bot To Idle Harder
---

The bot is going to click within the window, so I need to find where the window is on the screen. Since I'm programming this on Windows I'm going to use Python for Windows Extensions (http://sourceforge.net/projects/pywin32/). These extensions expose parts of the Windows API via python.

Using win32gui I can grab a handle to the clicker heroes window:

{% highlight py %}
win32gui.FindWindow(None, "Clicker Heroes")
{% endhighlight %}

Then with the handle I can grab the screen coordinates of the window:

{% highlight python %}
window_left, window_top, window_right, window_bottom = win32gui.GetWindowRect(window_handle)
{% endhighlight %}

This is groovy, but this rectangle includes the borders and header of the window. I can also grab the client area of the window, but this is isn't in screen coordinates. However, with these two functions and a bit of algebra I can figure out the client area in screen cooridnates:

{% highlight py %}
window_left, window_top, window_right, window_bottom = win32gui.GetWindowRect(window_handle)
client_left, client_top, client_right, client_bottom = win32gui.GetClientRect(window_handle)

window_width = window_right - window_left
client_width = client_right
border_width = (window_width - client_width) / 2

window_height = window_bottom - window_top
client_height = client_bottom
header_height = window_height - client_height - border_width

client_area_on_screen = (window_left + border_width, window_top + header_height,
                         window_right - border_width, window_bottom - border_width)
{% endhighlight %}

BREAK

In order to have the bot controllable by the user, even if it's not in focus, I'm going to need to grab input from the user. I'm going to do this by listening for key presses and if we get a specific press, killing the bot.

A relatively light weight way to do this is by registering a hot key. This involves asking Windows to send a key press to the bot program instead of sending it to the window in focus. This done by calling the RegisterHotKey function.

{% highlight py %}
win32gui.RegisterHotKey(None, 1, 0, win32con.VK_F11)
{% endhighlight %}
