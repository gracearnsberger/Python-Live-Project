## Python-Live-Project
-In a two week long sprint, I was able to work with other developers in order to build a collection of various apps that store different things in a project called "AppBuilder9000". This project was created using Python, html, js & css programming languages and using PyCharm Studio. My job was to create an app that uses both front end and back end development and will allow the user to browse, save, and view their favorite tropical cities that they find on my app. This project used Django Framework and was completed using Agile and Scrum methods in order to keep project management on course. 

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
                
   -In views.py I put in functions that take a web request and return a web response. 
   -I also used back end engineering to save the form from "Save Cities" page to the database which can be accessed and modified in SQL Server Management Studio.
   
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


   -I used Django Template Langage to override specific parts of my templates & displayed the Saved Cities for the specific user on the Tropical Wish List Page.
        
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
