# Live Project

## Introduction
For the last two weeks of my time at The Tech Academy, I worked with my peers in a team developing a full scale MCV/MVVM Web Application in C#. Working on a legacy codebase was a great learning opportunity for fixing bugs, cleaning up code, and adding requested features. There were some big changes that could have been a large time sink, but we used what we had to deliver what was needed on time. I saw how a good developer works with what they have to make a quality product. I worked on several [back end stories](#back-end-stories) that I am very proud of. Because much of the site had already been built, there were also a good deal of [front end stories](#front-end-stories) and UX improvements that needed to be completed, all of varying degrees of difficulty. Everyone on the team had a chance to work on front end and back end stories. Over the two week sprint I also had the opportunity to work on some other project management and team programming [skills](#other-skills-learned) that I'm confident I will use again and again on future projects.

Below are the descriptions of the stories that I worked on, along with code snippets and navigation links.

## Back End Stories
* [Custom BlogPost Table](#custom-blogpost-table)
* [Ajax Request to Delete BlogPosts](#ajax-request-story)
* [Custom DateTime Function](#custom-datetime-function)



### Custom BlogPost Table
This table was wrapped in a Foreach statement so that when the view is loaded it will go through the entire list of models I've created for BlogPosts and display them in their own containers one by one.

```
<!--Goes through each blog post saved in reverse order to post the most recent at the top-->
    @foreach (var item in Model.Reverse())
    {
      <tr id="tr @item.BlogPostId">
        <td id="BlogPost-index--postTableCell">
          <!--Start of the Blog Post. Creates a Row for each new one and displays them in a table-->
          <div class="row no-gutters" id="BlogPost-index--postContainer">

            <!--Start of Left Column, holds an image-->
            <div class="col" id="BlogPost-index--leftColumn">
              <img src="~/Areas/Blog/Data/img/test2.jpg" class="BlogPost-index--blogImages" alt="Test Image" />
            </div>
            <!--End Left Column-->
            <!--Start of Right Column, holds text relating to blog post-->
            <div class="col" id="BlogPost-index--rightColumn">
              <!--Contains the Author Name, Date Posted, and calculated time since it was posted.-->
              <div class="row" id="BlogPost-index--authorRow">
                <div class="col">
                  <p id="postInfo">
                    @Html.DisplayFor(model => item.Author)<br />
                    @Html.Raw(item.Posted.ToString("MMM d")) &#8226
                    @timeSincePosted(item.Posted)
                    <!--Calls the function at the top of the document to calculate and display time since post-->
                  </p>
                </div>
              </div>
              <!--End Author Name Row-->
              <!--Start of the BlogPost Title Row-->
              <div class="row" id="BlogPost-index--titleRow">
                <div class="col">
                  <h2>@Html.DisplayFor(model => item.Title)</h2>
                </div>
              </div>
              <!--End of the BlogPost Title Row-->
              <!--Start of the BlogPost Content Row-->
              <div class="row" id="BlogPost-index--blogRow">
                <div class="col">
                  <p id="BlogPost-index--blogContent">
                    @Html.DisplayFor(model => item.Content)
                  </p>
                </div>
              </div>
              <!--End of the BlogPost Content Row-->
              <!--Start of the BlogPost Button Row-->
              <div class="row" id="BlogPost-index--buttonRow">
                <!--Edit and Details Button Column-->
                <div class="col">
                  @Html.ActionLink(" Edit", "Edit", new { id = item.BlogPostId }, new { @class = "btn btn-secondary fas fa-edit" })
                  @Html.ActionLink(" Details", "Details", new { id = item.BlogPostId }, new { @class = "btn btn-secondary fas fa-info" })
                </div>
                <!--Delete Button Column-->
                <div class="col">
                  <button class="btn btn-danger fas fa-trash BlogPost-index--returnButton" data-toggle="modal" data-target="#BlogPost-index--deleteModal"
                  value="@item.BlogPostId">&nbsp;Delete</button>
                  <!--End of Button Row-->
                </div>
                <!--End of the BlogPost Button Row-->
              </div>
              <!--End of the Right Column-->
            </div>
          </div>
        </td>
      </tr>
      <!--End of the BlogPost Row-->
    } 
```
  
  
  
### Ajax Request Story
This story was part of our index page. Initially our page linked to a view that gave the user the option to delete the selected post, but instead we decided to integrate that   function into the same page that we were displaying the posts on. We used a BootStrap Modal to confirm whether or not the user would like to delete the page, and this is the     Ajax Request and Modal Close event used to handle that functionality. 
  
```
//Ajax function to Post to BlogPostController with the value taken from the Button creating the modal
$("#BlogPost-index--deleteModal").on('show.bs.modal', function (event) {
  let myUrl = $("#myUrl").val();
  let btn = $(event.relatedTarget);
  let id = btn.val();
  let modal = $(this);
  modal.find("#deleteButton").on("click", function (e) {
      $.ajax({
          type: "POST",
          url: myUrl,
          data: { Id: id },
          success: function () {
                  $(btn).closest("tr").hide();
                  $("#BlogPost-index--deleteModal").modal("hide");
                  $(".BlogPost-index--deleteNotif").fadeIn(500).delay(3000).fadeOut(2000);
          },
          error: function () {
              $("#BlogPost-index--deleteModal").modal("hide");
              let errorUrl = $("#notAuthorized").val();
              window.location.href(errorUrl);
      });
  });
})

//On Close Event for Modal that removes the listener from the DeleteButton that was applied above
$("#BlogPost-index--deleteModal").on('hide.bs.modal', function () {
  let modal = $(this);
  modal.find("#deleteButton").off();
})
//End of BlogPost JavaScript 
```



### Custom DateTime Function
This function takes the creation date and time from the selected object and compares it with a DateTime.Now object. Based on the difference measured in seconds it will return a string that neatly prints the time since created based on the result. This was a quality of life function I wanted to add onto our BlogPosts to give the users a clearer idea as to how long the BlogPost has been posted on our site. We were limited on the packages that were allowed to be added to the functions so instead of importing a package like Moment.Js I created my own using C#.

```
@functions
  {
  public string timeSincePosted(DateTime blogPostedTime)
  {
    var timeDifference = (DateTime.Now - blogPostedTime).TotalSeconds;
    var returnString = "";

    returnString = timeDifference >= 63113852 ? returnString = Math.Floor((timeDifference / 31556926)) + " years ago" :
                   timeDifference > 31556926 ? returnString = "1 year ago" :
                   timeDifference > 5259486 ? returnString = Math.Floor((timeDifference / 2629743)) + " months ago" :
                   timeDifference > 2629743 ? returnString = "1 month ago" :
                   timeDifference > 1209600 ? returnString = Math.Floor((timeDifference / 604800)) + " weeks ago" :
                   timeDifference > 604800 ? returnString = "1 week ago" :
                   timeDifference > 172800 ? returnString = Math.Floor((timeDifference / 86400)) + " days ago" :
                   timeDifference > 86400 ? returnString = "1 day ago" :
                   timeDifference > 7200 ? returnString = Math.Floor((timeDifference / 3600)) + " hours ago" :
                   timeDifference > 3600 ? returnString = "1 hour ago" :
                   timeDifference > 60 ? returnString = Math.Floor((timeDifference / 60)) + " minutes ago" :
                   returnString = timeDifference + " seconds ago";
    return returnString;
  }
}
```

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#live-project)*


## Front End Stories
* [Create and Edit BlogPost Pages](#create-and-edit-blogpost-pages)
* [Custom CSS Styling For All Pages](#custom-css-styling)



### Create and Edit BlogPost Pages
This is a demonstration of another page I was tasked with creating while working with our team. This will pull the ID from the BlogPost you selected via Controller and allow you to create a new BlogPost or edit an existing one depending on the selection on the previous page. I've lumped both of these views together seeing as the code is almost identical with the exception of the text being filled with existing variables from an old BlogPost, or just being empty.

```
<!--Start of Razer Form-->
  @using (Html.BeginForm()) {
    @Html.AntiForgeryToken()
    <!--Container for the entire form. Centered-->
    <div class="form-horizontal" id="BlogPost-Create-Edit--innerContainer">
     @Html.ValidationSummary(true, "", new { @class = "text-danger" })
     <!--Start of the Title Text and Input Form Div-->
      <div class="form-group" id="BlogPost-Create-Edit--titleGroup">
        @Html.LabelFor(model => model.Title, htmlAttributes: new { @class = "control-label col-md-4" })
       <div class="col">
         @Html.EditorFor(model => model.Title, new { htmlAttributes = new { @class = "form-control BlogPost-Create-Edit--textbox", placeholder = "Enter your title" } })
         @Html.ValidationMessageFor(model => model.Title, "", new { @class = "text-danger" })
       </div>
     </div>
     <!--End of the Title Text and Input Form Div-->
     <!--Start of the Content Text and Input Form Div-->
     <div class="form-group" id="BlogPost-Create-Edit--contentGroup">
       @Html.LabelFor(model => model.Content, htmlAttributes: new { @class = "control-label col-md-2" })
       <div class="col">
         <!--TextAreaFor Object to allow for a much better input experience-->
         @Html.TextAreaFor(model => model.Content, 8, 20, new { @class = "form-control BlogPost-Create-Edit--textbox", placeholder = "Enter your post", id = "BlogPost-Create-               Edit--contentBox" })
          @Html.ValidationMessageFor(model => model.Content, "", new { @class = "text-danger" })
       </div>
      </div>
      <!--End of the Content Text and Input Form Div-->
      <!--Start of the Posted Text and Input Form Div-->
      <div class="form-group" id="BlogPost-Create-Edit--postedGroup">
        @Html.LabelFor(model => model.Posted, htmlAttributes: new { @class = "control-label col-md-2" })
       <div class="col">
          @Html.EditorFor(model => model.Posted, new { htmlAttributes = new { @class = "form-control BlogPost-Create-Edit--textbox", placeholder = "Date posted" } })
          @Html.ValidationMessageFor(model => model.Posted, "", new { @class = "text-danger" })
       </div>
      </div>
      <!--End of the Posted Text and Input Form Div-->
      <!--Start of the Author Text and Input Form Div-->
      <div class="form-group" id="BlogPost-Create-Edit--authorGroup">
        @Html.LabelFor(model => model.Author, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col">
          @Html.EditorFor(model => model.Author, new { htmlAttributes = new { @class = "form-control BlogPost-Create-Edit--textbox", placeholder = "Enter your name" } })
          @Html.ValidationMessageFor(model => model.Author, "", new { @class = "text-danger" })
        </div>
      </div>
      <!--End of the Author Text and Input Form Div-->
      <!--Start of the Button Group Div-->
      <div class="form-group">
        <div class="col mx-auto">
         <input type="submit" value="Create" class="btn btn-block btn-warning BlogPost-Create-Edit--submitButton" />
         @Html.ActionLink("Back to List", "Index", null, new { @class = "btn btn-block btn-danger BlogPost-Create-Edit-backButton" })
       </div>
      </div>
      <!--End of the Button Group Div-->
    </div>
    <!--End of the Form Container-->
  }
```



### Custom CSS Styling
While on this project I made several views and had to adhere to a legacy styling format, but while doing so met a few complications with getting images and anything else that I needed to line up just enough to give the proper experience that I was hoping for. Here's a few snippets of the CSS work that I did while working on this project.

```
/*Start of BlogPost index styling*/
#BlogPost-index--bodyContainer {
    text-align: center
}

#BlogPost-index--authorRow,
#BlogPost-index--titleRow,
#BlogPost-index--blogRow {
    text-align: left;
    padding-left: 1vw;
    padding-right: 1vw;
}

#BlogPost-index--rightColumn {
    margin-left: 1vw;
}

.BlogPost-index--blogImages {
    max-width: 100.5%;
    height: auto;
    width: auto;
    height: auto;
    vertical-align: auto;
    border-style: none;
    border-radius: 0;
}

#BlogPost-index--authorRow {
    font-size: 12px;
    margin-top: 1.5vw;
}

#BlogPost-index--blogContent {
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: normal;
    display: -webkit-box;
    -webkit-line-clamp: 4;
    -webkit-box-orient: vertical;
    max-width: 450px;
}

#BlogPost-index--buttonRow {
    margin-top: 1.5vw;
    margin-bottom: 1vw;
}

#BlogPost-index--postContainer {
    border-radius: 10px;
    border: 2px solid #bd1a11;
    box-shadow: 1px 1px #bd1a11;
    max-height: 390px;
    max-width: 100%;
    overflow: hidden;
    margin: 1vw;
    min-width: 800px;
    overflow-x: hidden;
}
/*End of BlogPost index styling*/
```

And then for the Credit and Edit Pages we used these Stylings
```
/*Start of BlogPost Create and Edit Styling*/
/*Stylings for the Header, giving it a shadow and centering it*/
#BlogPost-Create-Edit--PageHeader {
    font-size: 3.5em;
    text-align: center;
    text-shadow: 1px 1px 3px #bd1a11, 2px 2px 1px #bd1a11, 3px 3px 1px #bd1a11;
    letter-spacing: 2px;
    word-spacing: 4px;
    margin-bottom: 2vw;
    margin-top: 1vw;
}

/*Setting the border and color for the container that is holding the form*/
#BlogPost-Create-Edit--formContainer {
    background-color: #fffbfb;
    border: 2px solid #bd1a11;
    box-shadow: 1px 2px 2px #bd1a11;
    border-radius: 8px;
}

/*Adding a margin above the title to give extra room between it and the border*/
#BlogPost-Create-Edit--titleGroup {
    margin-top: 1vw;
}

/*Changing the font color, weight, and size for each of the input forms*/
#BlogPost-Create-Edit--authorGroup,
#BlogPost-Create-Edit--contentGroup,
#BlogPost-Create-Edit--postedGroup,
#BlogPost-Create-Edit--titleGroup {
    color: #000000;
    font-weight: bold;
    font-size: 18px;
}

/*Adding padding around the form inside of the container so the text is spaced properly*/
#BlogPost-Create-Edit--innerContainer {
    padding-bottom: 5px;
    padding-top: 10px;
}

/*Adding padding to the bottom of the form between the buttons and Author Group*/
#BlogPost-Create-Edit--authorGroup {
    padding-bottom: 1em;
}

/*Adjusting the font size and font color for both buttons*/
.BlogPost-Create-Edit--submitButton.btn-warning,
.BlogPost-Create-Edit--submitButton.btn-warning:visited,
.BlogPost-Create-Edit--backButton.btn-danger,
.BlogPost-Create-Edit--backButton.btn-danger:visited {
    color: #fffbfb;
    font-size: 18px;
}

/*Changing the default background color and border color for the Submit button*/
.BlogPost-Create-Edit--submitButton.btn-warning {
    background-color: #d6972a;
    border-color: #d6972a;
}

/*On Hover, changing the background color and border color for the Submit button */
.BlogPost-Create-Edit--submitButton.btn-warning:hover {
    background-color: #9d7c39;
    border-color: #9d7c39;
}

/*If visited, changing the background color and border color for the Back to List button*/
.BlogPost-Create-Edit--backButton.btn-danger
.BlogPost-Create-Edit--backButton.btn-danger:visited {
    background-color: #f04d44;
    border-color: #f04d44;
}

/*On hover, changing the background color and border color for the Back to List button*/
.BlogPost-Create-Edit--backButton.btn-danger:hover {
    background-color: #bd1a11;
    border-color: #bd1a11;
}

/*Adjusting the text color for the pladeholder, and the input boxes.*/
.BlogPost-Create-Edit--textbox.form-control,
.BlogPost-Create-Edit--textbox.form-control::placeholder {
    color: #bd1a11;
}

/*Creating the normal background color for the input boxes*/
.BlogPost-Create-Edit--textbox.form-control {
    background-color: #E1e1e1;
}

/*Setting the Glow Border on focus for Text Boxes*/
.BlogPost-Create-Edit--textbox.form-control:focus {
    background-color: #fffbfb;
    box-shadow: 0px 1px 1px #bd1a11 inset, 0px 0px 8px #bd1a11;
    border-color: #bd1a11;
}

/*Turning off the Resize Option of the Content Box*/
#BlogPost-Create-Edit--contentBox {
    resize: none;
}
/*End of BlogPost Create Styling*/
```



### Other Skills Learned
* Working with a group of developers to identify front and back end bugs to improve usability of an application
* Improving project flow by communicating about who needs to check out which files for their current story
* Learning new efficiencies from other developers by observing their workflow and asking questions

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#live-project)*
