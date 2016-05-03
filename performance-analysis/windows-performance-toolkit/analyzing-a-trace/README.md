# Analyzing a Trace

## Open Windows Performance Analyzer (WPA)
Open Windows Performance Analyzer (WPA) then open the ETL file to be analyzed using File/Open.

## Load Symbols and Apply HTML Analysis Profile
**WARNING:** Loading symbols for the first time will require a large download and will take a significant amount of time on a typically internet connection.

**Tip:** Symbol loading can be made significantly faster by restricting symbol loading to Microsoft Edge and web apps. This is done by Trace/Configure Symbol Paths and altering Load Settings restricted to MicrosoftEdgeCP.exe and WWAHost.exe.

![Symbol Restrictions](../../media/WPA-SymbolRestrictions.PNG)

Immediately load symbols by choosing Trace/Load Symbols from the menu. The symbols will be cached to disk and future traces will load symbols much faster. After symbols begin loading, apply the Html Analysis Profile by selecting Profiles/Apply from the menu then clicking Browse Catalog. Choose HtmlResponsivenessAnalysis.wpaProfile and the profile will load a number of graphs and tables to allow analysis to begin. Profiles contain a set of graphs used for analysis. For nearly all web site investigations, we recommend starting with this profile.

![Big Picture](../../media/WPA-BigPicture.PNG)

## Zoom to remove Trace rundown
One important tip is to remove the Trace Rundown section by zooming in on the remaining section. This can be done by left clicking, holding and selecting the region to be zoomed. Then right click and choose Zoom. This will affect all graphs and charts on the active tab.

![Post Zoom](../../media/WPA-PostZoom.PNG)

## The Html Responsiveness Analysis Profile creates 4 tabs:
**Big Picture** - This is really valuable to confirm there are no unexpected sources of CPU activity and the browser indeed is using all available resources. Check CPU usage and confirm that no processes contribute significantly to CPU usage other than the browser.

**Frame Analysis** - This is the most used section for basic analysis. The CPU Usage (Attributed) graph enables a quick glance understanding of the subsystems responsible for CPU usage. Breaking down the samples in the CPU Usage (Sampled) table on the HTML UI Thread is most helpful for identifying critical performance bottlenecks.

**Thread Delay Analysis** - Typically used by Microsoft Edge developers to investigate when one thread is blocked and waiting on another. Rarely useful to web developers.

**Trace Markers** - This section would show all tracing markers coming from browser (Microsoft Edge) as well as  msWriteProfilerMark giving very precise points to measure code. To see msWriteProfilerMark tracing, go to Generic Events section and select HTML msWriteProfilerMark from the drop-down.

## In Frame Analysis, investigate what is using the CPU
In the Frame Analysis tab, the CPU Usage (Sampled) table is where most analysis is conducted. By expanding the various processes, the JavaScript and browser code that is taking the most CPU can be identified. It is very common that a single bit of JavaScript is responsible for a performance issue and a bit of work to optimize it can make a significant difference.

## Trick for bottom up DOM call analysis
Bottom up DOM call analysis can be useful for identifying the JavaScript code that is responsible for taking up the majority of time taken in a scenario. This is especially helpful when many top level calls are re-using the same JavaScript libraries.

Start by looking at CPU Usage (Sampled) Breakdown by Process, Thread, Activity, Stack. Click on any cell in Stack column. Press Ctrl+F and search for "ExternalFunctionThunk". (NOTE: This only works if you have successfully loaded symbols!)

![Search For ExternalFunctionThunk](../../media/WPA-ExternalFunctionThunk.png)

After finding any line with ExternalFunctionThunk (this is interface from the JavaScript engine, Chakra, to the Microsoft Edge engine. It shows where code bridges to browser from JavaScript execution. Right-click on it and select View Callees/By module. This gives a list of top browser engine functions with weights (in milliseconds).

![View Callees](../../media/WPA-ViewCallees.png)

To find the JavaScript calling that API, right click on it and select View Callers/By function then expand the tree to find the JavaScript responsible by weight.
