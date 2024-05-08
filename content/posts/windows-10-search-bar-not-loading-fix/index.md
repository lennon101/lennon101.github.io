+++
title = 'Windows 10 Search Bar Not Loading'
date = 2024-05-08T11:05:00+10:00
draft = false
categories = ['IT']
tags = ['windows10', 'how-to']
+++

## What happened 
The Search menu on my Windows 10 suddenly stopped working. When I clicked the Search icon or type something in the Start menu, it just shows a blank search window.
{{< figure src="/empty-search-bar.png" width="500" >}}

## The fix
1. Right click on the windows key and tap **Run**
{{< figure src="/open-run.png" width="300" >}}

2. Open the Registery Editor by typing **regedit** and tapping ok 
{{< figure src="/open-regedit.png" width="400" >}}

3. Navigate to: 

```
HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Search
```
{{< figure src="/search.png" width="600" >}}

4. Right-click the Search icon and choose New > DWORD (32-bit) Value. Name the new value: 
```
BingSearchEnabled
```
{{< figure src="/bing-enabled.png" width="600" >}}

5. Double-click the new BingSearchEnabled value to open its properties dialog. The number in the “Value data” box should already be 0. Just ensure it’s still 0. Click OK to continue. 
{{< figure src="/edit-word.png" width="600" >}}

6. Below `BingSearchEnabled`, you should see `CortanaConsent`. Double-click this value to open its properties dialog. Change its “Value Data” box to “0” aswell.

**Note Well:**
If you don’t see `CortanaConsent`, create it by following the same steps you used to create `BingSearchEnabled`.

{{< figure src="/cortana-consent.png" width="600" >}}

7. Restart `Explorer.Exe` by opening the `TaskManager`, right clicking on Windows Explorer, and tapping restart. The Windows Search Bar should now return to a working state 

{{< figure src="/restart-explorer.png" width="1000" >}}