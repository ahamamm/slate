## Extend Zenbox

Zenbox is a core feature in WorkZen, where users have an aggregated handy location to all pending tasks/actions.

Zenbox can be extended to include tasks and actions from external sources.

In this section We will walk you through steps needed to extend Zenbox GetToDoTasksPipeline functionality. 
To extend functionality we will need to create a  new C# project that contains static data sample and next edit configuration then deploy new artifacts to sitecore portal.

1. **C# project**
From your sitecore portal file system path  go to bin directory getfollowing DLLs
WorkZen.Foundation.ZenboxTasks.dll
WorkZen.Foundation.AzureAD.Authentication.dll
In your project add reference to both DLLs
Add a new class
Within a class add a new void method process
 process method takes one argument from type WorkZen.Foundation.ZenboxTasks.Pipelines. GetToDoPipelineArgs
public void Process(GetToDoPipelineArgs args)
Add your logic in the method

2. **Configuration**
From your sitecore portal file system path  go to bin directory getfollowing App_Config directory
Get WorkZen.Foundation.MSGraph.config file 
Update GetToDoTasksPipeline processor tag to be like
`<processor type="<class full name space>, <DLL name>" />`

3. **Deployment**
Upload your DLL file to  sitecore bin directory
Upload your configuration file to App_Config directory
Restart sitecore portal instance

4. **Sample**
Extract attached zip file
Dependencies DLLs are located in dependencies directory
Open solution using VS from <extractedfolder>\pathmyworkzen\src\myworkzen\myworkzen.extension.test  
