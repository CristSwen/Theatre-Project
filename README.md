# Theatre-Project
<h1>Introduction</h1>
<br>
<p>For the last two weeks of my time at the Tech Academy, I worked with my fellow students in a team developing a full scale<br>
ASP .NET MVC/MVVM Web Application in C#. We also ultilized Entity Framework. This project was an incredible learning opportunity.<br>
I was able to add many requested features, fix bugs, and clean up lots of code. This challenged my ability to deliver a certain<br>
amount of working code within a specific timeframe. This taught me a lot about communicating with a team of people to produce<br>
a great quality product. Because this was a group project, I was able to get into good version control habits. <br>
On my time on the project I participated in many front end and back end stories that you will see down below.</p>
<br>
<h2>Back End Stories</h2>
<br>
<ul>
  <li>Entity Model for CastMember and CRUD pages</li>
  <li>Photo Storage & Retrieval for CastMember</li>
</ul>
<br>
<h3>Entity Model for CastMember and CRUD pages</h3>
<br><p>My first task was creating a model for the Cast members. One of the properties needing to be an enum for what<br>
possible positions a Cast member could be. After the model was created, I updated the database and scaffolded the<br>
CRUD pages. </p>
<br>
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;

namespace TheatreCMS3.Areas.Prod.Models
{
    public class Castmember
    {
        public int CastMemberId { get; set; }
        public string Name { get; set; }
        public int? YearJoined { get; set; }
        public position MainRole { get; set; }
        public string Bio { get; set; }

        public byte[] Photo { get; set; }
        public bool CurrentMember { get; set; }
        public string Character { get; set; }
        public int? CastYearLeft { get; set; }
        public int? DebutYear { get; set; }
        public string ProductionTitle { get; set; }
        }

    public enum position
    {
        Actor,
        Director,
        Technician,
        StageManager,
        Other
    }
}
```
<br>
<h3>Photo Storage & Retrieval for CastMember</h3>
<br><p>For this story I was tasked with adding images to the CastMember Model. The idea is that when a user goes<br>
to create a new Cast Member on the website, they can upload an image and it will display on the details page. For this<br>
I first needed to create a method in the CastMember Controller that could retrieve the photo and convert it into<br>
a byte array.</p><br>
```
public byte[] PhotoConvert(HttpPostedFileBase photo)
        {
            byte[] bytes;
            using (BinaryReader br = new BinaryReader(photo.InputStream))
            {
                bytes = br.ReadBytes(photo.ContentLength);
            }
            return bytes;
        }
```
<br>
<p>I then went into the Create and Edit view pages and added a way for the user to upload an image of their choosing</p>
<br>
```
<div class="form-group">
            @Html.LabelFor(model => model.Photo, "Photo", htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.TextBoxFor(model => model.Photo, new {  type = "file", @class = "form-control" })
            </div>
        </div>
```
<br>
<p>Back in the CastMember Controller. I added the code to the Create method to use the PhotoConvert method.<br>
These had validation to check if there was or was not an image. If there was an image someone is tring to<br>
upload, call the PhotoConvert method and make the Photo parameter equal to the image.</p>
```
[HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "CastMemberId,Name,YearJoined,MainRole,Bio,CurrentMember,Character,CastYearLeft,DebutYear, Image")] Castmember castmember, HttpPostedFileBase Photo)
        {
            if (Photo != null && Photo.ContentLength > 0)
            {
                var photobyte = PhotoConvert(Photo);
                castmember.Photo = photobyte;
            }
            //var photobyte = PhotoConvert(Photo);
            if (ModelState.IsValid)
            {
                //castmember.Photo = photobyte;
                db.Castmembers.Add(castmember);
                db.SaveChanges();
                return RedirectToAction("Index");
            }

            return View(castmember);
        }        
```
<br>
<p>All that is left is for the image to display on the details page. I took the byte array and converted that<br>
into a string using an img tag and razer syntax so that the image can be displayed</p>
```
@if (Model.Photo != null)
                {
                    string words = Convert.ToBase64String(Model.Photo);
                    <div class="castmembers-detailsdelete--img">
                        <img class="card-img-top castmembers-index--image" src="data:image/jpeg;base64,@words" />
                    </div>
                 }
```
<br>
<h2>Front End Stories</h2>
<ul>
  <li>Sign-In page</li>
  <li>Create and Edit page</li>
  <li>Index/Details page</li>
  <li>Details and Delete</li>
</ul>
<br>
<h3>Sign-In page</h3>
<br><p>The Sign-In page hosts all the names of the developers who worked on the project. Each name added<br>
will now be contained within a grid across the page. The project had its own color theme as well. That is what<br>
the "var(--example-example--example);" is.</p><br>
```
.home-signin--container {
    color: var(--light-color);   
    text-align: center;
    display: grid;
    grid-template-columns: auto auto auto;
}

.home-signin--container p {
    background-color: var(--main-color--light);
    border-radius: 32px;
    width: 20em;
    padding: 2em;
}
```
<br>
<h3>Create and Edit page</h3>
<br><p>The Create and Edit forms were to be placed in the center. All input field borders were to change<br>
to one of the project theme colors as well. I was also tasked with adding background colors, hover efects<br>
, headers, and placeholders.</p>
```
.castmembers-createedit--submitbtn {
    background-color: var(--main-color--light);
    /*/border: none;
    padding: 15px 32px;*/
    color: white;
}

.castmembers-createedit-submitbtn:hover {
    background-color: var(--main-color);
    color: var(--light-color);
    font-weight: bold;
}

.castmembers-createedit--container {
    width: 60%;
    margin-left: 20%;
    border: 5px solid var(--secondary-color);
    background-color: var(--secondary-color--dark);
}

.castmembers-createedit-backtolistbtn {
    background-color: var(--secondary-color);
    color: white;
}

.castmembers-createedit-backtolistbtn:hover {
    color: var(--light-color);
}

.castmembers-createedit-label:focus {
    border: 2px solid var(--main-color);
}
```
<br>
<h3>Index/Details page</h3>
<br><p>For this story I was tasked with taking the images from the Back-End story and having them display like cards on the<br>
details page. The image will be 60% of the card while the bottom half is the name of the Cast Member. The Cast<br>
members should also be automatically sorted based on what production they were in. At the time the person tasked<br>
with making the model for the productions hadn't gotten to it yet. Because of that I had to create a temporary<br>
string property in the CRUD pages. Lastly if someone were to hover over the image, the edit and delete links<br>
would appear and if anywhere else on the image was clicked, it would take you to the details page.</p><br>
```
.castmembers-index--photocontainer {
    width: 18rem;
    text-align: center;
    position: relative;
    background-color: gray;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 15px 0 dimgray;
}

.castmembers-index--image {
    height: 12rem;
    display: block;
}

.editDelete {
    position: absolute;
    top: 50%;
    left: 28%;
    transform: translate(-50%, -50%);
    display: none;
    transform: scale(2,2);
    font-size: 13px;
    
}

.castmembers-index--photocontainer:hover .editDelete {
    display: block;
    /*filter: brightness(50%);*/
}

.castmembers-index--image:hover {
    filter: brightness(50%);
}
```
<br>
<h3>Details and Delete</h3>
<p>For the Details and Delete page, they both should display the image that was uplaoded for that specific Cast Member.<br>
Both pages should have everything centered as well. In this story I also made use of "Font Awesome Icons" to add Icons on the<br>
Edit, Delete, and Back to List buttons.</p>
<br>
```
.castmembers-detailsdelete--container {
    width: 60%;
    background-color: var(--main-color--light);
    margin-left: 20%;
    text-align: center;
    border-radius: 25px;
}

.castmembers-detailsdelete--img img {
    width: 50%;
    margin-left: auto;
    margin-right: auto;
    border-radius: 25px;
    border: 2px solid var(--main-color);
}

.castmembers-detailsdelete--textcontainer {
    border: double 2px;
    width: 80%;
    margin-left: auto;
    margin-right: auto;
    border: 2px solid var(--main-color);
}

.castmembers-detailsdelete--icons {
    background-color: var(--light-blue);
    text-align: center;
}
```
<br>
```
<p class="castmembers-detailsdelete--icons">
        <i class="fa fa-pencil-square-o">@Html.ActionLink(" Edit", "Edit", new { id = Model.CastMemberId })</i> |
        <i class="fa-solid fa-hand-point-left">@Html.ActionLink(" Back to List", "Index")</i>
</p>
```
<br>
<h2>Other Skills Learned</h2>
<ul>
  <li>Clean version control was a vital skill I learned, so that I could make sure there would be no merge<br>
  conflicts whenever I pushed my code. I learned how to do this through GitHub Desktop and on Visual Studio both.</li>
  <li>During the project I encountered numorous bugs with my code. Most of which I had never seen before.<br>
  A combination of using Google's Developer Tools, analzying the issue in pieces, StackOverflow, and asking<br>
  my fellow students for advice helped me succeed when I encountered unknown bugs.</li>
  <li>One major thing I learned during this project was communication. Every day after I was done with my code<br>
  I would always make sure to send in a Daily Report to my Instructor overseeing the project to let them know<br>
  what I accomplished that day, what I intend to do the next day, and if I had any roadblocks. I also reached<br>
  out any time I had an issue I couldn't quite understand. I also communicated with the other students on the<br>
  project so that we were always on the same page about the things we were doing whether that was front-end or<br>
  back-end.</li>
  <li>Lastly I learned about how to code more efficiently. When on a two week project sprint like this, I had to<br>
  manage my time carefully. For me to be able to get all that I needed to get done within the time constraint<br>
  , I needed to make sure I was being efficient on my stories. For me that meant breaking the story into parts.<br>
  I wouldn't move on to the next part of the story unless the first one was working. This meant that I wasn't<br>
  tackling multiple things at once and could focus all my energy into one thing at a time. At first I wasn't<br>
  great at that, but by the end of the project I was able to take a firm grasp on it.</li>
</ul>
