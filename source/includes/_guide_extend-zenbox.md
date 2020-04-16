## Extend Zenbox

Zenbox is a core feature in WorkZen, where users have an aggregated handy location to all pending tasks/actions.

Zenbox can be extended to include tasks and actions from external sources.

In this section We will walk you through steps needed to extend Zenbox GetToDoTasksPipeline functionality. 
To extend functionality we will need to create a  new C# project that contains static data sample and next edit configuration then deploy new artifacts to sitecore portal.

1. **C# project**
	* From your sitecore portal file system path  go to bin directory getfollowing DLLs
		* WorkZen.Foundation.ZenboxTasks.dll
		* WorkZen.Foundation.AzureAD.Authentication.dll
	* In your project add reference to both DLLs
	* Add a new class
	* Within a class add a new void method process
	* process method takes one argument from type WorkZen.Foundation.ZenboxTasks.Pipelines. GetToDoPipelineArgs
	* public void Process(GetToDoPipelineArgs args)
	* Add your logic in the method	
	
check sample code on the right side C# tab

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using MyWorkzen.Foundation.Custom.Services;
using WorkZen.Foundation.ZenboxTasks.Pipelines;
using WorkZen.Foundation.ZenboxTasks.Models;
using WorkZen.Foundation.AzureAD.Authentication.Helpers;

namespace MyWorkzen.Foundation.Custom.Pipelines
{
    public class GetToDoTasksPipeline
    {
        public void Process(GetToDoPipelineArgs args)
        {
            if (args.Tasks == null)
                args.Tasks = new List<ZenboxTask>();
            var claimIdentity = TokenHelper.GetClaimsIdentity();
            // First get user claims
            var claims = claimIdentity.Claims.ToList();

            //Filter specific claim    
            string user = claims?.FirstOrDefault(x => x.Type.Equals("UserName", StringComparison.OrdinalIgnoreCase))?.Value;

            ToDoService service = new ToDoService();
            args.Tasks.AddRange(service.getTodoTasks(user, args.Top, args.Skip, args.FilterBy, args.SortBy));
        }
    }
}
```	
	

2. **Configuration**
	* From your sitecore portal file system path  go to bin directory getfollowing App_Config directory
	* Get WorkZen.Foundation.MSGraph.config file 
	* Update GetToDoTasksPipeline processor tag to be like
	* `<processor type="<class full name space>, <DLL name>" />`
	
original XML document check right side XML tab
	
```xml
<configuration>
  <sitecore>myworkzen.extension.test
    <pipelines>
      <GetUserPayloadPipeline>
        <processor type="WorkZen.Foundation.MSGraph.Pipelines.MSGraphUserProfilePipeline, WorkZen.Foundation.MSGraph" />
      </GetUserPayloadPipeline>
      <GetToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.GetToDoTasksPipeline, myworkzen.extension.test" />
      </GetToDoTasksPipeline>
      <CompleteToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.CompleteToDoTasksPipeline, myworkzen.extension.test" />
      </CompleteToDoTasksPipeline>
      <CreateToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.CreateToDoTasksPipeline, myworkzen.extension.test" />
      </CreateToDoTasksPipeline>
      <DeleteToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.DeleteToDoTasksPipeline, myworkzen.extension.test" />
      </DeleteToDoTasksPipeline>
      <UpdateToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.UpdateToDoTasksPipeline, myworkzen.extension.test" />
      </UpdateToDoTasksPipeline>
      <ReOrderToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.ReorderToDoTasksPipeline, myworkzen.extension.test" />
      </ReOrderToDoTasksPipeline>
    </pipelines>
  </sitecore>
</configuration>
```

mdified XML document check right side XML tab
	
```xml
<configuration>
  <sitecore>myworkzen.extension.test
    <pipelines>
      <GetUserPayloadPipeline>
        <processor type="WorkZen.Foundation.MSGraph.Pipelines.MSGraphUserProfilePipeline, WorkZen.Foundation.MSGraph" />
      </GetUserPayloadPipeline>
      <GetToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.GetToDoTasksPipeline, myworkzen.extension.test" />
      </GetToDoTasksPipeline>
      <CompleteToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.CompleteToDoTasksPipeline, myworkzen.extension.test" />
      </CompleteToDoTasksPipeline>
      <CreateToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.CreateToDoTasksPipeline, myworkzen.extension.test" />
      </CreateToDoTasksPipeline>
      <DeleteToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.DeleteToDoTasksPipeline, myworkzen.extension.test" />
      </DeleteToDoTasksPipeline>
      <UpdateToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.UpdateToDoTasksPipeline, myworkzen.extension.test" />
      </UpdateToDoTasksPipeline>
      <ReOrderToDoTasksPipeline>
        <processor type="MyWorkzen.Foundation.Custom.Pipelines.ReorderToDoTasksPipeline, myworkzen.extension.test" />
      </ReOrderToDoTasksPipeline>
    </pipelines>
  </sitecore>
</configuration>
```

3. **Deployment**
Upload your DLL file to  sitecore bin directory
Upload your configuration file to App_Config directory
Restart sitecore portal instance

4. **Sample**
Extract attached zip file
Dependencies DLLs are located in dependencies directory
Open solution using VS from <extractedfolder>\pathmyworkzen\src\myworkzen\myworkzen.extension.test  