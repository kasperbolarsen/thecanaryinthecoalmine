<h1>The canary in the coalmine</h1>

About a year ago I was working on a SharePoint search based solution for a customer. Suttenly the content in the list and libraries didn't match the content in the search index anylonger, and we had to file a service request with Microsoft. After fighting our way through the 1st level support "Have you tried to reindex the site" we were contacted by a engineer that knew what was going on.

It seems like the refresh rate of the search index is based on a priority queue, and if your tenant is in the same farm as some tenents which are doing some heavy duty operations, then the reindex rate on your tenant is doing down.

In my case the refresh rate was fluctuating, but the avage was about once every 36-42 hours........


When asking about the SLA for search refresh rate I was informed that there isn't such a thing, any time is good enough. No wonder why MS isn't very public about this ;-)

Since the Microsoft 365 Health Dashboard don't report about any degradetion in the SharePoint refresh rate I desided the build my own Early Warning System, hence the name "Canary in the coalmine".

The solution is pretty simple and based on brute force:
In a SharePoint List I have a single item and the values is a DateTime.tick
In an Azure Function (PowerShell) the trigger fires every 5 minutes. The script reads the value of the list item, and compares it to the values for the same item in the search index. If the values are identical the tick in the SharePoint list is updated with a new tick, and the result of the comparison is logged in another SharePoint list. 
If the value in the search index doesn't match the list item within 30 minutes the alarm sounds, in this case by sending an email to the admin account. 

