# ASP.NetMVCLearnings and best practices.

## How to make detect the errors at compile time in views?
In views by default <MvcBuildViews> is set to false, to detect the errors in views we need to change this value to true like

```javascript
<MvcBuildViews> true </MvcBuildViews>.
Open the .csproj file of the solution in notepad search for the <MvcBuildViews> then set to true then save changes.
```
## How many ways we can pass data from controller to a view in mvc application?
* Using ViewBag or view Data. - We dont get compile time errors
* Using Dynamic type(@model dynamic (I.e we have to call like: @Model.Propertie) - We dont get compile time errors
* Using Strongly Typed View @model MVCDemo.Models.Employee(I.e we have to call like: @Model.Propertie) - We will get compile time errors

Advantages of using strongly typed views are intellisence and compile time error chacking.

## What is Partial view?
Partial view is nothing but some part of the view. like web usercontrol in asp.net web forms.
Partial views are used to encapsulate reusable view logic and are a great means to simplify the complexity of views. these partial views can be used in multiple views, where we need similar view logic.

Advantages: 
Make views simpler to undersatnd Amount of code will be redused.
Maintainability is much easier, no need to update every where this logic is used.

## What is the Difference between Partial and RenderPartial Html helper methods?
Both HtmlHelperMethods are designed to render the partial view.
Primary difference between both is return type of RenderPartial is void, where as partial is MvcHtmlString
Syntax for invoking  Partial html helper method is 

```javscript
@Html.Partial("PartialViewName",item)
Syntax for invoking  RenderPartial html helper method is 
{Html.RenderPartial("PartialViewName",item);} (becoz output of this function will return directly to the output Stream)
```

## When would you use Partial() over RenderPartial() and vice versa? Which one is better for Parformance?
When there is a need to assign output to varaible for manipulating it, then use Partial(), else use RenderPartial().
rendering directly to the output stream is better, so RenderPartial() does exactly.

File extension is for the aspx views it is ascx for both C# and vb 
For Razor View .cshtml for C#, .vbhtml for visual basic

## What are T4 Templates and their purpose?
T4 stands for Text Template Transformation Tool Kit and are used by visual studio to generate code when you add a view or controller.
Where does these templates live?
Controlers are in:
C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\ItemTemplates\CSharp\Web\MVC 4\CodeTemplates\AddController.
Views are in :
C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\ItemTemplates\CSharp\Web\MVC 4\CodeTemplates\AddView\CSHTML
What is the file extension for t4 files?
.tt
Is it possible to add your own t4 templates to the existing list of templates?
Absolutely Simply create a file with .tt file extension.

## What is Cross Site Scripting (xss)?
Cross-site scripting is one of the most dangerous website vulnerabilities. It is used in various ways to harm website users. Mostly it is used to perform session hijacking attacks. it is possible to resist from the hackers with using XSS filters.

## What is XSS Filters?
Preventing the  malicious characters from the user we will use XSS Filters.
To resist the malicious code from the hackers we will write the XSS filters.
Ex: While we are accepting the comments from the user they may write script and html code in that which may malicious

## How to accept htmlcode from the user?
By decorating the action method with [ValidateInput(false)] attribute, By using the @html.Raw(elementname) helper class.

to do this Lets take an example

If we want to provide user to enter html code we have to validate that in following manner
```javascript
[HttpPost]
[ValidateInput(false)]// this will prevent from validating the user input
public ActionResult MyMethod(Comments comment)
{
//to customize the user input prevention
StringBuilder sbComment= new StringBuilder();
sbComment.Append(HttpUtility.HtmlEncode(comment.Comments));
sbComment.Replace("&lt;b&gt;","<b>");// &lt is decoded form of< and &gt for >
sbComment.Replace("&lt;/b&gt;","</b>");
sbComment.Replace("&lt;u&gt;","<u>");
sbComment.Replace("&lt;/u&gt;","</u>");

comment.Comments=sbComments.Tostring();
//Now user can able to add bold, underline tags to comment text box if anything other than this can be encoded.
string strEncodedName= HttpUtility.HtmlEncode(comment.Name);
comment.Name=strEncodedName;
//then Add Create logic
if(ModelState.IsValid)
{
db.Comments.AddObject(comment);
db.SaveChanges();

return RedirectToAction("Index");
}
return View(comment);
}.
How to display accepted html code from the user.
By using @Html.Raw(item.Comments) instead @Html.DisplayFor(modelItem=> item.Comments)
```
## What is _ViewStart.cshtml?
Asp.NetMVC3, Has introduced _ViewStart.cshtml. Specify the layout Property in this file and place it in the views folder. All the views will then use the Layout file that is specified in _ViewStart.cshtml. This eliminates the need to specify the Layout Property on each and every view , and making more cleaned and maintainable.

If you want a set of views in a specific folder, to user a different layout file, then you can include another _ViewStart.cshtml file in that specific folder.

## When I use _ViewStart.cshtml file, can i still set layout property on individual views?
Yes, if you want to use a layout file  that os different from what is specified in _ViewStart.cshtml.

## Where else can i specify a layout file?
layout file can also be specified  in  a controller action method or in an action filter.
Public actionResult Create()
{
return View("ViewName","_SharedView");(i.e return view())
}

Using Layout File :
All shared view file names should be prefix with _ (its not rule but if we do that we can easily identify that is shared view) like _Layout File.
_Layout file will have Html code which can be sharable to all the views

_Layout file will have @Renderbody html helper. @ RenderBody will replace with the respective view when trying to the respective view. all the shared views can be in ~/Views/Shared/_sharedView.cshtml. root

If we want our shared view can be Renderd for all the views, then create _ViewStart.cshtml under the Views root. then Specify the Root in that.

Like:
@{
Layout="~/Views/Shared/_Layout.cshtml";
}

If We want our Home Controller Views should use other Shared View then 

Under the Views/ Home folder Create one _ViewStart.cshtml then Write

Like:
@{
Layout="~/Views/Shared/_OtherLayout.cshtml";
}

This will Override the ~/Views/_ViewStart.cshtml With ~/Views/Home/_ViewStart.cshtml so Now onwards we can see _OtherLayout.cshtml UI for the Home Views

If we want specific View should use Different Layout then 
 Open The View
@Model ......
@{
ViewBage.Title="Employee Page";
Layout="~/Views/Shared/_DifferentLayout.cshtml";
}


## How to make property as read only?
```javascript
go to View change @Html.EditFor(model=>model.name) to
 @Html.DisplayFor(model=>model.name)
 @Html.HiddenFor(model=>model.name)

This will
[post]
public actionresult Edit(int id)// changed from Employee e
{
EmployessBusinessLayer ebl= new EmployessBusinessLayer();
Employee employee=ebl.Employees.Single(x=>x.ID==id);
UpdateModel(employee, new string{"ID","Gender","City"});// this line will do the job of property readonly
if(ModelState.IsValid)
{
ebl.SaveEmployee(employee);
return RedirectToAction("Index");
}
```
## How Do you Prevent properties from being binded automatically?
```javascript
This can be done in a single line of code
UpdateModel<TModel>(TModel model,string prefix, string[] includeProperties,string[] excludeProperties);

By using two ways
1.Include list(White List) 

UpdateModel<TModel>(TModel model, string[] includeProperties);
ex:UpdateModel(employee,new string{"ID","Gender","City"});

2.Exclude List(Block List)

UpdateModel<TModel>(TModel model,string prefix, string[] includeProperties,string[] excludeProperties);
ex:UpdateModel(employee,null,null,new string{"Name"});

3.Using Bind Attribute
[post]
public actionresult Edit([Bind(include="ID,Gender,City")]Employee employee)// this line will do the job of property readonly or use([Bind(exclude="Name")]Employee employee)
{
EmployessBusinessLayer ebl= new EmployessBusinessLayer();
employee.Name=ebl.Employees.Single(x=>x.ID==employee.ID).Name;
if(ModelState.IsValid)
{
ebl.SaveEmployee(employee);
return RedirectToAction("Index");
}

4.Using Interfaces

We have below class
public class Emploee
{
public int ID{get;set;}
public string Name{get;set;}
[Required]
public string Gender{get;set;}
[Required]
public string City{get;set;}
}
then Create one Interface
***************public Interface IEmployee
{
int ID{get;set;}
string Gender{get;set;}
string City{get;set;}
}// here we dont have name propertie ********************
then 
Public class Employee:IEmployee
{
//define properties
}
then
[post]
public actionresult Edit(int id)// changed from Employee e
{
EmployessBusinessLayer ebl= new EmployessBusinessLayer();
Employee employee=ebl.Employees.Single(x=>x.ID==id);
************UpdateModel<IEmployee>(employee);// this line will do the job of property readonly*****************
if(ModelState.IsValid)
{
ebl.SaveEmployee(employee);
return RedirectToAction("Index");
}


Writing Delete Action Method
[HttpPost]


In View
<input type="submit" Value="Delete" onclick="return confirm('Are you sure you want to delte With ID=@item.ID')"/>
```
