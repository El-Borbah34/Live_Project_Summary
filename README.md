# Live Project Summary

## Introduction
The ability to solve complex problems on one's own is invaluable; during my last two weeks with the Tech Academy, I was lucky enough to experience this first hand. I participated as an intern in the development of an ASP.NET MVC application in C# in which we fixed bugs, cleaned up code, and added many request features. Some of our challenges were specifically designed to keep us on our toes, but the overarching goal was learning great time management and clean code in an Agile/Scrum environment. With this experience under my belt, I now understand how a good developer should be able to deliver quailty results. While a part of the Live Project, I worked on several [back end stories](#back-end-stories) that tested and strengthened my abilities to find my own solutions. I also navigated [front end stories](#front-end-stories) that dealt with UI/UX improvements of varying difficulty. My participation in this internship helped to develop [other skills](#other-skills) for further projects and team programming skills that will be used throughout my coding career.
 
Below are descriptions of the stories I worked on, along with code snippets and navigation links. I also have some files from the project in this repo so you can examine the bigger picture behind functionality that I implemented.


## Back End Stories
* [Auto-Populate Production's Default Photo](#auto-populate-productions-default-photo)
* [Seed Production Photos](#seed-production-photos)
* [Sorting User List Table](#sort-user-list-table)

### Auto-Populate Production's Default Photo

Our Web Application contained theater productions, each production need to be checked if a default photo had been assigned and if not assign it the current photo that is being created.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "Title,Description,Production")] ProductionPhotos productionPhotos,                           HttpPostedFileBase file)
        {
            int productionID = Convert.ToInt32(Request.Form["Productions"]);
           
            productionPhotos.PhotoId = PhotoController.CreatePhoto(file, productionPhotos.Title);

            if (ModelState.IsValid)
            {
                Production production = db.Productions.Find(productionID);
                productionPhotos.Production = production;

                if (production.DefaultPhoto == null)
                {
                    production.DefaultPhoto = productionPhotos;
                }

                db.ProductionPhotos.Add(productionPhotos);
                db.SaveChanges();      

                return RedirectToAction("Index");
            }
            
            return View(productionPhotos);
        }

### Seed Production Photos

The application's Startup.cs page needed to be modified so our team could see photos for each production upon default. I provided ten photos in total that were sized and scaled properly, two for each production. After storing the photos to the database I used the same seed to then set a default photo for each theater production using Entity Framework.

        private void SeedProductionPhotos()
        {
            var converter = new ImageConverter();
            string imagesRoot = Path.Combine(AppDomain.CurrentDomain.BaseDirectory + @"\Content\Images");

            Image image1 = Image.FromFile(Path.Combine(imagesRoot, @"hamilton1.png"));
            Image image2 = Image.FromFile(Path.Combine(imagesRoot, @"hamilton2.png"));
            Image image3 = Image.FromFile(Path.Combine(imagesRoot, @"phantom1.png"));
            Image image4 = Image.FromFile(Path.Combine(imagesRoot, @"phantom2.png"));
            Image image5 = Image.FromFile(Path.Combine(imagesRoot, @"bookofmormon1.png"));
            Image image6 = Image.FromFile(Path.Combine(imagesRoot, @"bookofmormon2.png"));
            Image image7 = Image.FromFile(Path.Combine(imagesRoot, @"wicked1.png"));
            Image image8 = Image.FromFile(Path.Combine(imagesRoot, @"wicked2.png"));
            Image image9 = Image.FromFile(Path.Combine(imagesRoot, @"howtosucceedinbusinesswithoutreallytrying.png"));
            Image image10 = Image.FromFile(Path.Combine(imagesRoot, @"howtosucceedinbusinesswithoutreallytrying2.png"));

            var photos = new List<Photo>
            {
                new Photo
                {
                    OriginalHeight = image1.Height,
                    OriginalWidth = image1.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image1, typeof(byte[])),
                    Title = "Hamilton Image 1"
                },
                new Photo
                {
                    OriginalHeight = image2.Height,
                    OriginalWidth = image2.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image2, typeof(byte[])),
                    Title = "Hamilton Image 2"
                },
                new Photo
                {
                    OriginalHeight = image3.Height,
                    OriginalWidth = image3.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image3, typeof(byte[])),
                    Title = "Phantom Of The Opera Image 1"
                },
                new Photo
                {
                    OriginalHeight = image4.Height,
                    OriginalWidth = image4.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image4, typeof(byte[])),
                    Title = "Phantom Of The Opera Image 2"
                },
                new Photo
                {
                    OriginalHeight = image5.Height,
                    OriginalWidth = image5.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image5, typeof(byte[])),
                    Title = "The Book of Mormon Image 1"
                },
                new Photo
                {
                    OriginalHeight = image6.Height,
                    OriginalWidth = image6.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image6, typeof(byte[])),
                    Title = "The Book of Mormon Image 2"
                },
                new Photo
                {
                    OriginalHeight = image7.Height,
                    OriginalWidth = image7.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image7, typeof(byte[])),
                    Title = "Wicked Image 1"
                },
                new Photo
                {
                    OriginalHeight = image8.Height,
                    OriginalWidth = image8.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image8, typeof(byte[])),
                    Title = "Wicked Image 2"
                },
                new Photo
                {
                    OriginalHeight = image9.Height,
                    OriginalWidth = image9.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image9, typeof(byte[])),
                    Title = "How to Succeed in Business Without Really Trying Image 1"
                },
                new Photo
                {
                    OriginalHeight = image10.Height,
                    OriginalWidth = image10.Width,
                    PhotoFile = (byte[])converter.ConvertTo(image10, typeof(byte[])),
                    Title = "How to Succeed in Business Without Really Trying Image 2"
                },

            };
            photos.ForEach(Photo => context.Photo.AddOrUpdate(p => p.PhotoFile, Photo));
            context.SaveChanges();
            var productionphoto = new List<ProductionPhotos>
            {
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "Hamilton Image 1").FirstOrDefault().PhotoId,
                    Title = "Hamilton",
                    Description = "Actors performing \"The Story Of Tonight\".",
                    Production = context.Productions.Where(name => name.Title == "Hamilton").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "Hamilton Image 2").FirstOrDefault().PhotoId,
                    Title = "Hamilton",
                    Description = "Hamilton Cover.",
                    Production = context.Productions.Where(name => name.Title == "Hamilton").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "Phantom Of The Opera Image 1").FirstOrDefault().PhotoId,
                    Title = "Phantom Of The Opera",
                    Description = "Phantom Of The Opera Cover.",
                    Production = context.Productions.Where(name => name.Title == "Phantom of the Opera").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "Phantom Of The Opera Image 2").FirstOrDefault().PhotoId,
                    Title = "Phantom Of The Opera",
                    Description = "The Phantom and Christine embrace.",
                    Production = context.Productions.Where(name => name.Title == "Phantom of the Opera").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "The Book of Mormon Image 1").FirstOrDefault().PhotoId,
                    Title = "The Book of Mormon",
                    Description = "Kevin Price main stage.",
                    Production = context.Productions.Where(name => name.Title == "The Book of Mormon").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "The Book of Mormon Image 2").FirstOrDefault().PhotoId,
                    Title = "The Book of Mormon",
                    Description = "General Butt-F******-Naked and Kevin Price singing together.",
                    Production = context.Productions.Where(name => name.Title == "The Book of Mormon").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "Wicked Image 1").FirstOrDefault().PhotoId,
                    Title = "Wicked",
                    Description = "Wicked Cover",
                    Production = context.Productions.Where(name => name.Title == "Wicked").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "Wicked Image 2").FirstOrDefault().PhotoId,
                    Title = "Wicked",
                    Description = "Elphaba Thropp in the spotlight.",
                    Production = context.Productions.Where(name => name.Title == "Wicked").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "How to Succeed in Business Without Really Trying Image                           1").FirstOrDefault().PhotoId,
                    Title = "How to Succeed in Business Without Really Trying",
                    Description = "Smitty and the other secretaries read up on \"How to Hook a Tycoon and Take Him For All He’s                             Worth\".",
                    Production = context.Productions.Where(name => name.Title == "How to Succeed in Business Without Really                                 Trying").FirstOrDefault()
                },
                new ProductionPhotos
                {
                    PhotoId = context.Photo.Where(photo => photo.Title == "How to Succeed in Business Without Really Trying Image                           2").FirstOrDefault().PhotoId,
                    Title = "How to Succeed in Business Without Really Trying",
                    Description = "Daniel Radcliffe as Pierrepont Finch.",
                    Production = context.Productions.Where(name => name.Title == "How to Succeed in Business Without Really                                 Trying").FirstOrDefault()
                }
            };
            productionphoto.ForEach(prodphoto => context.ProductionPhotos.AddOrUpdate(p => p.PhotoId, prodphoto));
            context.SaveChanges();
            
            var productions = context.Productions.ToList();
            var productionPhotos = context.ProductionPhotos.ToList();
            foreach (var production in productions)
            {
                production.DefaultPhoto = productionPhotos.Where(productionPhoto => productionPhoto.Production ==                                       production).FirstOrDefault();
            }
            context.SaveChanges();
        }
       
### Sort User List Table

The page contained a table of all different type of users and I was assigned to make the table sortable by several different catagories, this was done without the need to reload the page.

        public ActionResult UserList(string requestedSort = "UserName", string currentSortOrder = "")
        {
            string newSortOrder = currentSortOrder;
            var users = db.Users.AsNoTracking().ToList();
            
            if (newSortOrder.Contains(requestedSort))
            {
                if (newSortOrder.Contains("_desc"))
                {
                    newSortOrder = newSortOrder.Replace("_desc", "");
                }
                else
                {
                    newSortOrder += "_desc";
                }
            }
            else
            {
                newSortOrder = requestedSort;
            }

            switch (newSortOrder)
            {
                case "UserName":
                    users = users.OrderBy(user => user.UserName).ToList();
                    break;
                case "UserName_desc":
                    users = users.OrderByDescending(user => user.UserName).ToList();
                    break;
                case "FirstName":
                    users = users.OrderBy(user => user.FirstName).ToList();
                    break;
                case "FirstName_desc":
                    users = users.OrderByDescending(user => user.FirstName).ToList();
                    break;
                case "LastName":
                    users = users.OrderBy(user => user.LastName).ToList();
                    break;
                case "LastName_desc":
                    users = users.OrderByDescending(user => user.LastName).ToList();
                    break;
                case "Role":
                    users = users.OrderBy(user => user.Role).ToList();
                    break;
                case "Role_desc":
                    users = users.OrderByDescending(user => user.Role).ToList();
                    break;
                default:
                    ViewBag.SortOrder = currentSortOrder;
                    return View(users);
            }
            ViewBag.SortOrder = newSortOrder;
            return View(users);
        }
        

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills), [Page Top](#live-project-summary)*

## Front End Stories
* [Fix Footer Sizing](#fix-footer-sizing)
### Fix Footer Sizing
This story was a fix to the page's footer. I enabled the ability to resize desite what screen was currently viewing the content as well as more responsiveness to the footer.


*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills), [Page Top](#live-project-summary)*

## Other Skills
* The ability to work with a group of developers to identify front and back end bugs to the improve usability.
* Improvement to project flow by communicating about where help is needed and if anyone has experienced the same road-blocks.
* Learning new efficiencies from other developers by observing their workflow and asking questions. 
* Practice with team programming/pair programming when one developer runs into a bug they cannot solve
    * More than one developer, including myself, hit many a road-blocks during our time with this application. I found myself in our groups "problem-solving" channel in Slack asking my peers questions ,with the help of others, I was able to solve my problem as well as being even great notes on how to further my assignment to completion.
    * The developers had the option to gather on Tuesdays and Thursdays via Google Meetup. There we were allowed to openly discuss goals, road-blocks and developments in our stories. 
    

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills), [Page Top](#live-project-summary)*
