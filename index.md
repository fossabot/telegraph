---
layout: default
---
![Aaawww~ :3](assets/cover.jpg)

**Telegra.ph** is a minimalist publishing tool that allows you to create richly formatted posts and push them to the Web in just a click. **Telegraph** posts also get beautiful [Instant View](https://telegram.org/blog/instant-view) pages on **Telegram**.

At first, you need to download/update and install it:  
`$ go get -u github.com/toby3d/go-telegraph`

And import it in your Go code:  
`import "github.com/toby3d/go-telegraph"`

#### [1. Methods](#available-methods)
- [CreateAccount]
- [CreatePage]
- [EditAccountInfo]
- [EditPage]
- [GetAccountInfo]
- [GetPage]
- [GetPageList]
- [GetViews]
- [RevokeAccessToken]

#### [2. Types](#available-types)
- [Account]
- [Node]
- [NodeElement]
- [Page]
- [PageList]
- [PageViews]

#### [3. Content format](#content-format)

-----

### Available methods
Library used **POST** HTTP method. The response contains a JSON object, which always has a Boolean field `ok`. If `ok` equals *true*, the request was successful, and the result of the query can be found in the `result` field. In case of an unsuccessful request, `ok` equals *false*, and the error is explained in the error field (e.g. SHORT_NAME_REQUIRED). All queries must be made using UTF-8.

#### CreateAccount
Use this method to create a new Telegraph account. Most users only need one account, but this can be useful for channel administrators who would like to keep individual author names and profile links for each of their channels. On success, returns an [Account] object with the regular fields and an additional `access_token` field.

- **account** (*[Account]*)  
Telegraph account.

> **Sample code**
> 
>     newAccount, err := telegraph.CreateAccount(&telegraph.Account{
>         ShortName:  "Sandbox",
>         AuthorName: "Anonymous",
>     })

#### EditAccountInfo
Use this method to update information about a Telegraph account. Pass only the parameters that you want to edit. On success, returns an [Account] object with the default fields.

- **update** (*[Account]*)  
Telegraph account.

> **Sample code**
> 
>     updatedAccount, err := account.EditAccountInfo(&telegraph.Account{
>         ShortName:  "Sandbox",
>         AuthorName: "Anonymous",
>     })

#### GetAccountInfo
Use this method to get information about a Telegraph account. Returns an [Account] object on success.

- **fields** (*...string*)  
List of account fields to return. Available fields: *short_name*, *author_name*, *author_url*, *auth_url*, *page_count*.

> **Sample code**
> 
>     accountInfo, err := account.GetAccountInfo("short_name", "page_count")

#### RevokeAccessToken
Use this method to revoke access_token and generate a new one, for example, if the user would like to reset all connected sessions, or you have reasons to believe the token was compromised. On success, returns an [Account] object with new `access_token` and `auth_url` fields.

> **Sample code**
> 
>     updatedAccount, err := account.RevokeAccessToken()

#### CreatePage
Use this method to create a new Telegraph page. On success, returns a [Page] object.

- **page** (*[Page]*)  
Page on Telegraph.
- **returnContent** (*bool*)  
If *true*, a `content` field will be returned in the [Page] object (see: [Content format](#content-format)).

> **Sample code**
> 
>     newPage, err := account.CreatePage(&telegraph.Page{
>         Title:      "Sample Page",
>         AuthorName: account.AuthorName,
>         Content:    content,
>     }, true)


#### EditPage
Use this method to edit an existing Telegraph page. On success, returns a [Page] object.

- **update** (*[Page]*)  
Page on Telegraph.
- **returnContent** (*bool*)  
If *true*, `content` field will be returned in [Page] object.

> **Sample code**
> 
>     updatedPage, err := account.EditPage(&telegraph.Page{
>         Title:      "Sample Page",
>         AuthorName: account.AuthorName,
>         Content:    content,
>     }, true)

#### GetPage
Use this method to get a Telegraph page. Returns a [Page] object on success.

- **path** (*string*)  
*Required.* Path to the Telegraph page (in the format `Title-12-31`, i.e. everything that comes after `http://telegra.ph/`).
- **returnContent** (*bool*)  
If *true*, `content` field will be returned in [Page] object.

> **Sample code**  
> 
>     page, err := telegraph.GetPage("Sample-Page-12-15", true)

#### GetPageList
Use this method to get a list of pages belonging to a Telegraph account. Returns a [PageList] object, sorted by most recently created pages first.

- **offset** (*int*)
Sequential number of the first page to be returned.
- **limit** (*int, 0-200*)
Limits the number of pages to be retrieved.

> **Sample code**
> 
>     pages, err := account.GetPageList(0, 3)

#### GetViews
Use this method to get the number of views for a Telegraph article. Returns a [PageViews] object on success. By default, the total number of page views will be returned.

- **path** (*string*)  
*Required.* Path to the Telegraph page (in the format `Title-12-31`, where 12 is the month and 31 the day the article was first published).
- **hour** (*int, 0-24 or -1*)  
If passed, the number of page views for the requested hour will be returned.
- **day** (*int, 1-31 or 0*)  
*Required if hour is passed.* If passed, the number of page views for the requested day will be returned.
- **month** (*int, 1-12 or 0*)  
*Required if day is passed.* If passed, the number of page views for the requested month will be returned.
- **year** (*int, 2000-2100 or 0*)  
*Required if month is passed.* If passed, the number of page views for the requested year will be returned.

> **Sample code**
> 
>     views, err := telegraph.GetViews("Sample-Page-12-15", -1, 0, 12, 2016)

### Available types
All types used in the Telegraph API responses are represented as JSON-objects. Optional fields may be `nil` when irrelevant.

#### Account
This object represents a Telegraph account.

- **AccessToken** (*string*)  
*Optional. Only returned by the [CreateAccount] and [RevokeAccessToken] method.* Access token of the Telegraph account.
- **AuthURL** (*string*)  
*Optional.* URL to authorize a browser on [telegra.ph](http://telegra.ph) and connect it to a Telegraph account. This URL is valid for only one use and for 5 minutes only.
- **ShortName** (*string*)  
Account name, helps users with several accounts remember which they are currently using. Displayed to the user above the "Edit/Publish" button on Telegra.ph, other users don't see this name.
- **AuthorName** (*string*)  
Default author name used when creating new articles.
- **AuthorURL** (*string*)  
Profile link, opened when users click on the author's name below the title. Can be any link, not necessarily to a Telegram profile or channel.
- **PageCount** (*int*)  
*Optional.* Number of pages belonging to the Telegraph account.

#### PageList
This object represents a list of Telegraph articles belonging to an account. Most recently created articles first.

- **TotalCount** (*int*)  
Total number of pages belonging to the target Telegraph account.
- **Pages** (*\[\][Page]*)  
Requested pages of the target Telegraph account.

#### Page
This object represents a page on Telegraph.

- **AuthorName** (*string*)  
*Optional.* Name of the author, displayed below the title.
- **AuthorURL** (*string*)  
*Optional.* Profile link, opened when users click on the author's name below the title. Can be any link, not necessarily to a Telegram profile or channel.
- **CanEdit** (*bool*)  
*Optional. Only returned if AccessToken passed. True*, if the target Telegraph account can edit the page.
- **Content** (*\[\][Node]*)  
*Optional.* [Content](#content-format) of the page.
- **Description** (*string*)  
Description of the page.
- **ImageURL** (*string*)  
*Optional.* Image URL of the page.
- **Path** (*string*)  
Path to the page.
- **Title** (*string*)  
Title of the page.
- **URL** (*string*)  
URL of the page.
- **Views** (*int*)  
Number of page views for the page.

#### PageViews
This object represents the number of page views for a Telegraph article.

- **Views** (*int*)  
Number of page views for the target page.

#### Node
This abstract object represents a DOM Node. It can be a *string* which represents a DOM text node or a [NodeElement] object.

- **Node** (*interface{}*)

#### NodeElement
This object represents a DOM element node.

- **Attrs** (*map\[string\]string*)  
*Optional.* Attributes of the DOM element. Key of object represents name of attribute, value represents value of attribute. Available attributes: *href, src*.
- **Children** (*\[\][Node]*)  
*Optional.* List of child nodes for the DOM element.
- **Tag** (*string*)  
Name of the DOM element. Available tags: *a, aside, b, blockquote, br, code, em, figcaption, figure, h3, h4, hr, i, iframe, img, li, ol, p, pre, s, strong, u, ul, video*.

### Content format
The Telegraph API uses a DOM-based format to represent the content of the page. Below is an example of code in Go which explains how you can use it:

    const data = ```
        <figure>
            <img src="http://telegra.ph/file/6a5b15e7eb4d7329ca7af.jpg"/>
        </figure>
        <p>
            <i>Hello</i>, my name is <b>Page</b>, <u>look at me</u>!
        </p>
        <figure>
            <iframe src="https://youtu.be/fzQ6gRAEoy0"></iframe>
            <figcaption>
                Yes, you can embed youtube, vimeo and twitter widgets too!
            </figcaption>
        </figure>
    ```
    
    content, err := telegraph.ContentFormat(data)

[CreateAccount]: #createaccount
[CreatePage]: #createpage
[EditAccountInfo]: #editaccountinfo
[EditPage]: #editpage
[GetAccountInfo]: #getaccountinfo
[GetPage]: #getpage
[GetPageList]: #getpagelist
[GetViews]: #getviews
[RevokeAccessToken]: #revokeaccesstoken
[Account]: #account
[Node]: #node
[NodeElement]: #nodeelement
[Page]: #page
[PageList]: #pagelist
[PageViews]: #pageviews
[Page]: #page
