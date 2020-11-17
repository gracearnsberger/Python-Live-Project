## Python-Live-Project
-In a two week long sprint, I was able to work with other developers in order to build a collection of various apps that store different things in a project called "AppBuilder9000". This project was created using Python, HTML, JS & CSS programming languages and using PyCharm Studio. My job was to create an app that uses both front end and back end engineering and will allow the user to browse, save, and view their favorite tropical cities that they find on my app. I used back end engineering to save the form on my "Saved Cities" page to the database which can be accessed and modified in SQL Server Management Studio and I used front end engineering to make the site user friendly, organized, and all linked together. This project used Django Framework & Template Language and was completed using Agile and Scrum methods in order to keep project management on course. 

 -I used the urls.py file to register each page and the url path associated 
   
        #url paths for each page
        urlpatterns = [
            path('home', views.tropical_cities_home, name='TropicalCitiesHome'),
            path('popular_cities', views.tropical_cities_popular, name='TropicalCitiesPopular'),
            path('save_tropical_cities', views.save_tropical_cities, name='SaveTropicalCities'),
            path('wish_list_index', views.tropical_cities_wish_list_index, name='TropicalCitiesWishList'),
            path('book_hotels', views.tropical_cities_book_hotels, name='TropicalCitiesBookHotels'),
            path('city-details/<int:pk>/', views.tropical_cities_details, name='save_cities'),
        ]
       
 -The user can browse cities on the popular cities page and then choose to save a city which will bring them to the save cities page where they will fill out a form full of information. The saved form data will then be displayed under the Wish List page which will show the saved cities with a details button. When the details button is clicked it will take the user to the city details page with the remaining information from the form such as rating, why, and the date it was saved.
 
 ![Beach App Home](https://lh3.googleusercontent.com/-iViq-Z0cWjc/X7PxsE1LuqI/AAAAAAAAIWU/g5PVIfd8aeEetw7foL7HPAGgSJxJtzLpQCK8BGAsYHg/s512/2020-11-17.png)  
 ![Beach App Popular Cities](https://lh3.googleusercontent.com/-w8CBHwGwhyc/X7P3BpcSlTI/AAAAAAAAIWo/cR7Z8LsPSx03pjhV4fJVQjd4HuVY6kroQCK8BGAsYHg/s512/2020-11-17.png)  

-In my models.py file I set up a form where on the "Save Cities" page, the user can select which city they want to save, rate it 1-10, write why they chose that city, and the date that they saved it which will then be saved to the database and will be displayed under the "Saved Tropical Wishlist" page for the user to browse.

    from django.db import models

    #pick which city you want to save
    city_choices = [
        ('Bora Bora','Bora Bora'),
        ('Kauai','Kauai'),
        ('Miami','Miami'),
        ('Barcelona','Barcelona'),
        ('Valparaiso','Valparaiso'),
        ('Sydney','Sydney'),
        ('Cape Town','Cape Town'),
        ('Singapore','Singapore'),
        ('Dubai','Dubai'),
        ('Los Angeles','Los Angeles'),
    ]

    city_rating = [
        ('0','0'),
        ('1','1'),
        ('2','2'),
        ('3','3'),
        ('4','4'),
        ('5','5'),
        ('6','6'),
        ('7','7'),
        ('8','8'),
        ('9','9'),
        ('10','10'),
    ]


    #fields for form on savecities.html page

    class savecities(models.Model):
        city_name = models.CharField(max_length=75, choices=city_choices)
        why_here = models.CharField(max_length=200, blank=False)
        date_saved = models.DateField()
        rate_city = models.CharField(max_length=75, choices=city_rating)

        objects = models.Manager()

    #return city name

        def __str__(self):
            return self.city_name
            
  
   ![Beach App Save Cities](https://lh3.googleusercontent.com/-Y5vF_0e4-vc/X7PwiJqiQ5I/AAAAAAAAIWE/VWDMQy1FxLgJ2O8CaG8C-549-h-9-n9SgCK8BGAsYHg/s512/2020-11-17.png)
   
   -In forms.py file, the form on the "Save Cities" page is registered 
   
         #form for save cities page
        class DateInput(forms.DateInput):
                input_type = 'date'

        class CityForm(ModelForm):
            class Meta:
                model = savecities
                fields = '__all__'
                widgets = {
                    'date_saved': DateInput()
                }
            
   -Using the html page for "Save Cities" I used the form method "POST" to save the information that the user inputs
   
         {% block header %}
        Save Cities to Your Tropical City Wish List! &#127965
        {% endblock %}

        <!--model form-->
        {% block content %}
            <div class="form">
                        <form method="POST" action="save_tropical_cities">
                            <div>
                                {% csrf_token %}
                                {{ form.as_ul }}
                                <input type= "submit" value="Save">
                            </div>
                        </form>
            </div>
        {% endblock %}
                
   -In views.py I put in functions that take a web request and return a web response for each page
   
         #home page
        def tropical_cities_home(request):
            return render(request, 'BeachApp/BeachApp_home.html')


        #popular cities page
        def tropical_cities_popular(request):
            return render(request, 'BeachApp/BeachApp_pop_cities.html')


        #save cities page
        def save_tropical_cities(request):
            form = CityForm(request.POST or None)
            if form.is_valid():
                form.save()
                return redirect('TropicalCitiesHome')
            else:
                print(form.errors)
                form = CityForm()

            context = {'form': form}
            return render(request, 'BeachApp/BeachApp_save_tropical_cities.html', context)


        #view saved cities page
        def tropical_cities_wish_list_index(request):
            mycities = savecities.objects.all()
            return render(request, 'BeachApp/BeachApp_wish_list_index.html', {"mycities": mycities})


        def tropical_cities_details(request, pk):
            pk = int(pk)
            save_cities = savecities.objects.get(id=pk)
            return render(request, 'BeachApp/BeachApp_details.html', {"save_cities" : save_cities})

        #hotels page
        def tropical_cities_book_hotels(request):
            return render(request, 'BeachApp/BeachApp_hotels.html')


   -I used Django Template Langage to override specific parts of my templates 
   -The users saved cities will be displayed on the Tropical Wish List Page with the city name and a "details" button which will take the user to the details page with the    remaining information.
        
         {% block header %}
        Saved Tropical Cities Details!
        {% endblock %}

        {% block content %}
        <!--display database on cards on index page-->
        <div class="card-container text-center">
            <div class="card text-white bg-info mb-3 col-md-6" style="max-width: 18rem;">
              <div class="card-body">
                <h7 class="card-subtitle">Why?: {{save_cities.why_here}}</h7>
                <h6 class="card-subtitle-two mb-3 text-white">Date: {{save_cities.date_saved}}</h6>
                <h5 class="card-subtitle-three mb-3 text-white">Rating: {{save_cities.rate_city}}</h5>
              </div>
                <a class="btn btn-danger btn-lg btn-block" href="javascript:history.back(-1)" role="button">Back</a>
            </div>
        </div>
        {% endblock %}
        
![Beach App Wish List](https://lh3.googleusercontent.com/-vx0RrkFJ21g/X7P2xmzmtAI/AAAAAAAAIWg/MYvTGE6Wf8U2GnVuwdbEN0uKeC7NS0bXwCK8BGAsYHg/s512/2020-11-17.png)   
![Beach App Details](https://lh3.googleusercontent.com/-uQ4__knxAPM/X7P4R9DtkpI/AAAAAAAAIW0/WV7DbufOGR4NNmPsQDzzPfx1SYKqv_WLwCK8BGAsYHg/s512/2020-11-17.png)
