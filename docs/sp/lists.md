# @pnp/sp/lists

Lists in SharePoint are collections of information built in a structural way using columns and rows. Columns for metadata, and rows representing each entry. Visually, it reminds us a lot of a database table or an Excel spreadsheet. 

## ILists

[![](https://img.shields.io/badge/Invokable-informational.svg)](../invokable.md) [![](https://img.shields.io/badge/Selective%20Imports-informational.svg)](../selective-imports.md)

|Scenario|Import Statement|
|--|--|
|Selective 1|import { sp } from "@pnp/sp";<br />import { Webs, IWebs } from "@pnp/sp/src/webs"; <br />import { Lists, ILists } from "@pnp/sp/src/lists";|
|Selective 2|import { sp } from "@pnp/sp";<br />import "@pnp/sp/src/webs";<br />import "@pnp/sp/src/lists";|
|Preset: All|import { sp, Lists, ILists } from "@pnp/sp/presets/all";|
|Preset: Core|import { sp, Lists, ILists } from "@pnp/sp/presets/core";|

### Get List by Id

Gets a list from the collection by id (guid). Note that the library will handle a guid formatted with curly braces (i.e. '{03b05ff4-d95d-45ed-841d-3855f77a2483}') as well as without curly braces (i.e. '03b05ff4-d95d-45ed-841d-3855f77a2483'). The Id parameter is also case insensitive.

```TypeScript
import { sp } from "@pnp/sp";
import "@pnp/sp/src/webs";
import "@pnp/sp/src/lists";

// get the list by Id
const list = sp.web.lists.getById("03b05ff4-d95d-45ed-841d-3855f77a2483");

// we can use this 'list' variable to execute more querys on the list:
const r = await list.select("Title")();

// show the response from the server
console.log(r.Title);
```

### Get List by Title

You can also get a list from the collection by title.

```TypeScript
import { sp } from "@pnp/sp";
import "@pnp/sp/src/webs";
import "@pnp/sp/src/lists";

// get the default document library 'Documents'
const list = sp.web.lists.getByTitle("Documents");

// we can use this 'list' variable to run more querys on the list:
const r = await list.select("Id")();

// log the list Id to console
console.log(r.Id);
```

### Add List

You can add a list to the web's list collection using the .add-method. To invoke this method in its most simple form, you can provide only a title as a parameter. This will result in a standard out of the box list with all default settings, and the title you provide. 

```TypeScript
// create a new list, passing only the title
const listAddResult = await sp.web.lists.add("My new list");

// we can work with the list created using the IListAddResult.list property:
const r = await listAddResult.list.select("Title")();

// log newly created list title to console
console.log(r.Title);
});
```

You can also provide other (optional) parameters like description, template and enableContentTypes. If that is not enough for you, you can use the parameter named 'additionalSettings' which is just a TypedHash, meaning you can sent whatever properties you'd like in the body (provided that the property is supported by the SharePoint API).

```TypeScript
// this will create a list with template 101 (Document library), content types enabled and show it on the quick launch (using additionalSettings)
const listAddResult = await sp.web.lists.add("My Doc Library", "This is a description of doc lib.", 101, true, { OnQuickLaunch: true });

// get the Id of the newly added document library
const r = await listAddResult.list.select("Id").get();

// log id to console
console.log(r.Id);
```

### Ensure that a List exists (by title)

Ensures that the specified list exists in the collection (note: this method not supported for batching). Just like with the add-method (see examples above) you can provide only the title, or any or all of the optional paramters desc, template, enableContentTypes and additionalSettings. 

```TypeScript
// ensure that a list exists. If it doesn't it will be created with the provided title (the rest of the settings will be default):
const listEnsureResult = await sp.web.lists.ensure("My List");

// check if the list was created, or if it already existed:
if (listEnsureResult.created) {
    console.log("My List was created!");
} else {
    console.log("My List already existed!");
}

// work on the created/updated list
const r = await listEnsureResult.list.select("Id")();

// log the Id
console.log(r.Id);
```

If the list already exists, the other settings you provide will be used to update the existing list.

```TypeScript
// add a new list to the lists collection of the web
sp.web.lists.add("My List 2").then(async () => {

// then call ensure on the created list with an updated description
const listEnsureResult = await sp.web.lists.ensure("My List 2", "Updated description");

// get the updated description
const r = await listEnsureResult.list.select("Description")();

// log the updated description
console.log(r.Description);
});
```

### Ensure Site Assets Library exist

Gets a list that is the default asset location for images or other files, which the users upload to their wiki pages.

```TypeScript
// get Site Assets library
const siteAssetsList = await sp.web.lists.ensureSiteAssetsLibrary();

// get the Title
const r = await siteAssetsList.select("Title")();

// log Title
console.log(r.Title);
```

### Ensure Site Pages Library exist

Gets a list that is the default location for wiki pages.

```TypeScript
// get Site Pages library
const siteAssetsList = await sp.web.lists.ensureSitePagesLibrary();

// get the Title
const r = await siteAssetsList.select("Title")();

// log Title
console.log(r.Title);
```

## IList

[![](https://img.shields.io/badge/Invokable-informational.svg)](../invokable.md) [![](https://img.shields.io/badge/Selective%20Imports-informational.svg)](../selective-imports.md)

|Scenario|Import Statement|
|--|--|
|Selective 1|import { sp } from "@pnp/sp";<br />import { List, IList } from "@pnp/sp/src/lists";|
|Selective 2|import { sp } from "@pnp/sp";<br />import "@pnp/sp/src/lists";|
|Preset: All|import { sp, List, IList } from "@pnp/sp/presets/all";|
|Preset: Core|import { sp, List, IList } from "@pnp/sp/presets/core";|

### Update a list

Update an existing list with the provided properties. You can also provide an eTag value that will be used in the IF-Match header (default is "*")

```TypeScript
import { IListUpdateResult } from "@pnp/sp/src/lists";

// create a TypedHash object with the properties to update
const updateProperties = {
    Description: "This list title and description has been updated using PnPjs.",
    Title: "Updated title",
};

// update the list with the properties above
list.update(updateProperties).then(async (l: IListUpdateResult) => {

    // get the updated title and description
    const r = await l.list.select("Title", "Description")();

    // log the updated properties to the console
    console.log(r.Title);
    console.log(r.Description);
});
```

### Get changes on a list

From the change log, you can get a collection of changes that have occurred within the list based on the specified query.

```TypeScript
import { sp, IChangeQuery } from "@pnp/sp";

// build the changeQuery object, here we look att changes regarding Add, DeleteObject and Restore
const changeQuery: IChangeQuery = {
    Add: true,
    ChangeTokenEnd: null,
    ChangeTokenStart: null,
    DeleteObject: true,
    Rename: true,
    Restore: true,
};

// get list changes
const r = await list.getChanges(changeQuery);

// log changes to console
console.log(r);
```

### Get list items using a CAML Query

You can get items from SharePoint using a CAML Query.

```TypeScript
import { ICamlQuery } from "@pnp/sp/src/lists";

// build the caml query object (in this example, we include Title field and limit rows to 5)
const caml: ICamlQuery = {
    ViewXml: "<View><ViewFields><FieldRef Name='Title' /></ViewFields><RowLimit>5</RowLimit></View>",
};

// get list items
const r = await list.getItemsByCAMLQuery(caml);

// log resulting array to console
console.log(r);
```

If you need to get and expand a lookup field, there is a spread array parameter on the getItemsByCAMLQuery. This means that you can provide multiple properties to this method depending on how many lookup fields you are working with on your list. Below is a minimal example showing how to expand one field (RoleAssignment)

```TypeScript
import { ICamlQuery } from "@pnp/sp/src/lists";

// build the caml query object (in this example, we include Title field and limit rows to 5)
const caml: ICamlQuery = {
    ViewXml: "<View><ViewFields><FieldRef Name='Title' /><FieldRef Name='RoleAssignments' /></ViewFields><RowLimit>5</RowLimit></View>",
};

// get list items
const r = await list.getItemsByCAMLQuery(caml, "RoleAssignments");

// log resulting item array to console
console.log(r);
```

### Get list items changes using a Token

```TypeScript
import {  IChangeLogItemQuery } from "@pnp/sp/src/lists";

// build the caml query object (in this example, we include Title field and limit rows to 5)
const changeLogItemQuery: IChangeLogItemQuery = {
    Contains: `<Contains><FieldRef Name="Title"/><Value Type="Text">Item16</Value></Contains>`,
    QueryOptions: `<QueryOptions>
    <IncludeMandatoryColumns>FALSE</IncludeMandatoryColumns>
    <DateInUtc>False</DateInUtc>
    <IncludePermissions>TRUE</IncludePermissions>
    <IncludeAttachmentUrls>FALSE</IncludeAttachmentUrls>
    <Folder>My List</Folder></QueryOptions>`,
};

// get list items
const r = await list.getListItemChangesSinceToken(changeLogItemQuery);

// log resulting XML to console
console.log(r);
```

### Recycle a list

Removes the list from the web's list collection and puts it in the recycle bin.

```TypeScript
await list.recycle();
```

### Render list data

```TypeScript
import { IRenderListData } from "@pnp/sp/src/lists";

// render list data, top 5 items
const r: IRenderListData = await list.renderListData("<View><RowLimit>5</RowLimit></View>");

// log array of items in response
console.log(r.Row);
```

### Render list data as stream

```TypeScript
import { IRenderListDataParameters } from "@pnp/sp/src/lists";

// setup parameters object
const renderListDataParams: IRenderListDataParameters = {
    ViewXml: "<View><RowLimit>5</RowLimit></View>",
};

// render list data as stream
const r = await list.renderListDataAsStream(renderListDataParams);

// log array of items in response
console.log(r.Row);
```

### Reserve list item Id for idempotent list item creation

```TypeScript
const listItemId = await list.reserveListItemId();

// log id to console
console.log(listItemId);
```

### Get list item entity type name

```TypeScript
const entityTypeFullName = await list.getListItemEntityTypeFullName();

// log entity type name
console.log(entityTypeFullName);
```

### Add a list item using path (folder), validation and set field values

```TypeScript
const list = await sp.webs.lists.getByTitle("MyList").select("Title", "ParentWebUrl")();
const formValues: IListItemFormUpdateValue[] = [
                {
                    FieldName: "Title",
                    FieldValue: title,
                },
            ];
            
list.addValidateUpdateItemUsingPath(formValues,`${list.ParentWebUrl}/Lists/${list.Title}/MyFolder`)

```
## content-types imports

|Scenario|Import Statement|
|--|--|
|Selective 1|import "@pnp/sp/src/content-types";|
|Selective 2|import "@pnp/sp/src/content-types/list";|
|Preset: All|import { sp } from "@pnp/sp/presets/all";|

### contentTypes

Get all content types for a list
```TypeScript
const list = sp.web.lists.getByTitle("Documents");
const r = await list.contentTypes();
```

## fields imports

|Scenario|Import Statement|
|--|--|
|Selective 1|import "@pnp/sp/src/fields";|
|Selective 2|import "@pnp/sp/src/fields/list";|
|Preset: All|import { sp } from "@pnp/sp/presets/all";|

### fields

Get all the fields for a list

```TypeScript
const list = sp.web.lists.getByTitle("Documents");
const r = await list.fields();
```

Add a field to the site, then add the site field to a list

```TypeScript
const fld = await sp.site.rootWeb.fields.addText("MyField");
await sp.web.lists.getByTitle("MyList").fields.createFieldAsXml(fld.data.SchemaXml);
```

## folders imports

|Scenario|Import Statement|
|--|--|
|Selective 1|import "@pnp/sp/src/folders";|
|Selective 2|import "@pnp/sp/src/folders/list";|
|Preset: All|import { sp } from "@pnp/sp/presets/all";|

### folders

Get the root folder of a list.

```TypeScript
const list = sp.web.lists.getByTitle("Documents");
const r = await list.rootFolder();
```

## forms imports

|Scenario|Import Statement|
|--|--|
|Selective 1|import "@pnp/sp/src/forms";|
|Selective 2|import "@pnp/sp/src/forms/list";|
|Preset: All|import { sp } from "@pnp/sp/presets/all";|

### forms

```TypeScript
const r = await list.forms();
```

## items imports

Scenario|Import Statement
--|--
Selective 1|import "@pnp/sp/src/items";
Selective 2|import "@pnp/sp/src/items/list";
Preset: All|import { sp } from "@pnp/sp/presets/all";

### items

Get a collection of list items.

```TypeScript
const r = await list.items();
```

## views imports

Scenario|Import Statement
--|--
Selective 1|import "@pnp/sp/src/views";
Selective 2|import "@pnp/sp/src/views/list";
Preset: All|import { sp } from "@pnp/sp/presets/all";

### views

Get the default view of the list

```TypeScript
const list = sp.web.lists.getByTitle("Documents");
const views = await list.views();
const defaultView = await list.defaultView();
```

Get a list view by Id

```TypeScript
const view = await list.getView(defaultView.Id).select("Title")();
```

## security imports

# TODO:: link to the security page and document all there, no need to duplicate

## subscriptions imports

Scenario|Import Statement
--|--
Selective 1|import "@pnp/sp/src/subscriptions";
Selective 2|import "@pnp/sp/src/subscriptions/list";
Preset: All|import { sp } from "@pnp/sp/presets/all";

### subscriptions

Get all subscriptions on the list

```TypeScript
const list = sp.web.lists.getByTitle("Documents");
const subscriptions = await list.subscriptions();
```

## user-custom-actions imports

Scenario|Import Statement
--|--
Selective 1|import "@pnp/sp/src/user-custom-actions";
Selective 2|import "@pnp/sp/src/user-custom-actions/web";
Preset: All|import { sp } from "@pnp/sp/presets/all";

## userCustomActions

Get a collection of the list's user custom actions.

```TypeScript
const list = sp.web.lists.getByTitle("Documents");
const r = await list.userCustomActions();
```