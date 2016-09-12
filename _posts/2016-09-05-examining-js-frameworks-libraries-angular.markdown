---
layout: post
title:  "Examining JS Frameworks/Libraries - AngularJS"
date:   2016-09-12 15:10:47 -0500
categories: javascript hackreactor angular libraries frameworks
---

This is part of a 'series' of sorts. My previous post focused on React and can be **[found here]**. <!--excerpt-->

Quick disclaimer, this is not meant to be a tutorial. For the past weeks at HackReactor we've been diving into a couple of these frameworks/libraries and I thought that in addition to this helping me solidify my understanding of them, seeing example code can hopefully help others picking up these frameworks/libraries explore what makes each one unique and when/why you may want to use one over the other depending on your project. Let's get started with AngularJS below.

You can find the code for the weather app on my **[GitHub here]**.

To view the app in action, **[click here]**.

In the early stages of learning Angular, I found it to be a bit frustrating beacause it feels like a lot of the work is abstracted. I was spending more time trying to understand the layout of the framework and less time coding. My goal here will be to try to help you quickly understand the framework, so you can see how powerful it is, and get to building sooner.  
Angular is an incredibly robust framework that allows you to write a lot less code than you would otherwise need to were you to try to achieve the same outcomes using vanilla JavaScript or JQuery. It's most popularly used to create single page applications (SPA). Single page applications are exactly what the name implies, it is an app where only one page, index.html, is served and the rest of the routing is conducted by the front end framework. To a user, it will seem like a normal, non-single page app - if they click on profile, they'll see the url change to .../profile, but there wasn't any HTTP request sent like there normally is on non-single page apps.

### Views

The views render information to the web page. In the case of single page application, we have one page that is served up - index.html - and subsequent views (in Angular, these are html docs) that are displayed to the user. 

The index.html serves as the entry point. It loads all the angular modules we need, as well as our scripts and styles. That's not really any different than what any normal index.html does. Well, there are a few more things you add. 

1. 'ng-app' - lets Angular know this is the part of the html it needs to pay attention to. It's often added to the <html> tag.
2. 'ui-view' - lets Angular and its router know where to insert html that it is told to render or hide.

**index.html**

{% highlight html %}
    <!DOCTYPE html>
    <html ng-app='app'>
    <head>
        <meta charset="utf-8">
        <title>Weather App</title>
        <link rel="stylesheet" href="styles/style.css">
        <link href='https://fonts.googleapis.com/css?family=Lato:400,100,300,700' rel='stylesheet' type='text/css'>

        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.7/angular.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-router/0.3.1/angular-ui-router.min.js"></script>
    </head>
    <body>
        <main ui-view>

        </main>

        <script src="app/displayWeather.js"></script>
        <script src="app/search.js"></script>
        <script src="app/services/services.js"></script>
        <script src="app/config.js"></script>
        <script src="app/app.js"></script>
    </body>
    </html>
{% endhighlight %}

Our other two sub-views will be search.html, where the user enters a city to be searched, and displayWeather.html, that will display the results of the search.

**searchForm.html**

{% highlight html %}
    <div id='search' className='clearfix'>
        <h2>Weather Forecast</h2>
        <form name="searchCity" ng-submit="search()">
            <input ng-model="city" type='text' placeholder='Type city name here...' />
            <input type='submit' value='Go'/>
        </form>
    </div>
{% endhighlight %}

**displayWeather.html**

{% highlight html %}
    <div>
        <h2>City Name</h2>
        <div id="todaysWeather">
            <h3>Today</h3>
            <img src="" />
            <div class='mainTemp'></div>
            <div class='minTemp'><span class='orange'>Min:</span> </div>
            <div class='maxTemp'><span class='orange'>Max:</span> </div>
        </div>
    </div>
    <ul id='weeksWeather' class='clearfix'>
        <li>
            <h4>Weekday</h4>
            <img src=""/>
            <div class='mainTemp'>Main Temp</div>
        </li>
    </ul>
{% endhighlight %}

### Modules, Controllers & $scope

We have these static html pages now that show a form and some information, but we need a way to interact with them. The form will need to be able to take the city name the user typed in and use it in the API call to search for that city's weather and the displayWeather.html needs to display the corresponding data that is received from the API call. As is, these documents are not able to do that. 

This is where controllers and $scope come in handy. Controllers facilitate the flow of data and $scope is the model. $scope is an object, created within the controller, whose properties and methods are accessible from within the controller's corresponding view (HTML doc).

And what about modules? Angular is modular. It allows us to separate specific app functionality into 'modules', making them resusable. We use modules to contain our controllers so the $scope in the controller that deals with the search form does not collide with the $scope in the controller responsible for rendering the weather data. 

Let's look at what the barebones controller looks like for 'searchForm.html' and 'displayWeather.html'.

**search.js**

{% highlight js %}
    angular.module('app.search', [])
    .controller('SearchController', function($scope){
        $scope.city;

        $scope.search = function($scope.city){
            // some function that sends request to api 
            // using city user typed in
        }
    });
{% endhighlight %}

**displayWeather.js**

{% highlight js %}
    angular.module('app.displayWeather', [])
    .controller('DisplayWeatherController', function($scope){
        // will hold data that we get from API
        $scope.weather = {};

        // temperature comes back in Kelvin
        $scope.farenheit = function(K){
            var f = (K-273.15)*1.8000 + 32;
            return Math.round(f);
        };

        // day comes back as a timestamp
        $scope.getDay = function(d) {
            var day = new Date(d * 1000);
            var numDay = day.getDay();
            var days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
            return days[numDay];
        }

        // puts together the url for the weather icons
        $scope.makeUrl = function(url){
            return "http://openweathermap.org/img/w/" + url + ".png";
        }

    });
{% endhighlight %}

#### Directives & Expressions

I said that properites and methods on $scope are accessible from within the controllers corresponding view (HTML) - directives and expressions are what you use within your HTML to access it. 

Directives begin with 'ng-' and many exist - see [here for a list]. One you'll see used in our searchForm.html is 'ng-submit'. We use it to specify what method we want called when a form is submitted. Another popular one is 'ng-model' that binds the input to the corresponding property in the $scope. 

**searchForm.html**
{% highlight html %}
    <div id='search' className='clearfix'>
        <h2>Weather Forecast</h2>
        <form name="searchCity" ng-submit="search()">
            <input ng-model="city" type='text' placeholder='Type city name here...' />
            <input type='submit' value='Go'/>
        </form>
    </div>
{% endhighlight %}

Expressions are essentially JavaScript written between {{ }} that gets evaluated. 

We know the data coming back from the API will look something like:

{% highlight js %}
    {"city":
        {
            "id":4930956,
            "name":"Boston",
            "coord":{"lon":-71.059769,"lat":42.358429},
            "country":"US",
            "population":0
        },
    "cod":"200",
    "message":0.0098,
    "cnt":7,
    "list":[
        {
            "dt":1471104000,
            "temp":{
                "day":293.86,
                "min":293.86,
                "max":293.86,
                "night":293.86,
                "eve":293.86,

    . . .
{% endhighlight %}

We are storing the data in the SearchController as $scope.weather. We can then use expressions to extract what data we want from within displayWeather.html.

**displayWeather.html**

{% highlight html %}
    <div>
        <a class="button" href="#/search">Back to Search</a>
        <h2>{{ weather.city.name }}</h2>
        <div id="todaysWeather">
            <h3>Today</h3>
            <img src="{{ makeUrl(weather.list[0].weather[0].icon) }}" />
            <div class='mainTemp'>{{ farenheit(weather.list[0].temp.day) }}&deg;</div>
            <div class='minTemp'><span class='orange'>Min:</span> {{ farenheit(weather.list[0].temp.min) }}&deg;</div>
            <div class='maxTemp'><span class='orange'>Max:</span> {{ farenheit(weather.list[0].temp.max) }}&deg;</div>
        </div>
    </div>
    <ul id='weeksWeather' class='clearfix'>
        <li ng-repeat="item in weather.list">
            <h4>{{ getDay(item.dt) }}</h4>
            <img ng-src="{{ makeUrl(item.weather[0].icon) }}" />
            <div class='mainTemp'>{{ farenheit(item.temp.day) }}&deg;</div>
        </li>
    </ul>
{% endhighlight %}

A directive used above that comes in handy is 'ng-repeat'. It is essentially the equeivalent to the 'for in' loop. A new <li> will be created for each item in the weather.list array.

#### Services

The very first time I played around with Angular and tried to create a small app, I put everything that could possibly fit in the controller. But that defeats the purpose. 

You'll quickly see why you shouldn't when you try to persist data. Controllers, like components in React, are dismantled when they are not in use. Any data stored in $scope at that point is scrubbed clean. 

There's an article, **'[Angular .service() or .factory(), the actual answer]'**, that explains this very clearly. 

To give you a quick summary (though I suggest you take a look at the article):

1. Service - think pseudoclassical instantiation pattern. A service is a constructor that is invoked using the 'new' keyword. It's properties are made available to the controller by passing the service in as an argument.
2. Factory - you create and return an object whose properties become available to the controller by passing the factory in as an argument. You are not bound by any one instantiation pattern.

So where would we want to use a service in this weather app? We need to be able to store the data that comes back from the api call to then display it elsewhere. I've decided to use a factory here that holds a function that fetches the weather data and a variable that will hold what is sent back.

**services.js**

{% highlight js %}
    angular.module('app.services', [])
    .factory('WeatherAPI', ['$http', '$location', function($http, $location){

        var weather;

        var getWeather = function(city){
            return $http({
                method: 'GET',
                url: `http://api.openweathermap.org/data/2.5/forecast/daily?q=${city},us&APPID=${window.API_KEY}`
            })
            .then(function (response) {
                return response.data;
            });
        };

        return {
            getWeather: getWeather,
            weather: weather
        };
    }]);
{% endhighlight %}

We can now update our controllers.

**search.js**

{% highlight js %}
    angular.module('app.search', [])
    .controller('SearchController', ['$scope', '$location', 'WeatherAPI', function($scope, $location, WeatherAPI){
        $scope.city;

        $scope.search = function(){
            WeatherAPI.getWeather($scope.city)
                .then(function(data){
                    // Store fetched data in factory's weather variable
                    WeatherAPI.weather = data;
                    $location.path('/weather');
                })
        }
    }]);
{% endhighlight %}

**displayWeather.js**

{% highlight js %}
    angular.module('app.displayWeather', [])
    .controller('DisplayWeatherController', ['$scope', '$location', 'WeatherAPI', function($scope, $location, WeatherAPI){
        
        $scope.weather = WeatherAPI.weather;

        $scope.farenheit = function(K){
            var f = (K-273.15)*1.8000 + 32;
            return Math.round(f);
        };

        $scope.getDay = function(d) {
            var day = new Date(d * 1000);
            var numDay = day.getDay();
            var days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
            return days[numDay];
        }

        $scope.makeUrl = function(url){
            return "http://openweathermap.org/img/w/" + url + ".png";
        }
    }]);
{% endhighlight %}

### Routing & app.js

Let's look at app.js that brings it all together. We inject all our various modules we've created and include 'ui.router'. This is a powerful Angular module that helps out with routing and rendering. 

Using AngularUI Router, we can decide what views to render when. There's a whole lot more you can do with it as well, but we're keeping it simple here.

**app.js**

{% highlight js %}
    angular.module('app', [
        'ui.router',
        'app.displayWeather',
        'app.search',
        'app.services'
    ])
    .config(function($stateProvider, $urlRouterProvider){
        $urlRouterProvider.otherwise('/search');

        $stateProvider
        .state('search', {
            url: '/search',
            templateUrl: 'app/searchForm.html',
            controller: 'SearchController'
        })
        .state('weather', {
            url: '/weather',
            templateUrl: 'app/displayWeather.html',
            controller: 'DisplayWeatherController'
        });

    });   
{% endhighlight %}

### Wrap Up

It may be a bit tough to tell from such a simple app, but Angular is quite a powerful framework. I can understand some peoples frustrations with it in feeling that it takes over the reins too much. But that also means that it takes care of a lot of the minute details allowing you to focus on functionality. 

Having now worked some with Angular, React, and Backbone, I found that React was the easiest to pick up but that I had an easier time scaling up an app's functionality with Angular. However, that can also be due to the fact that these technologies aim to fill different voids. Backbone was the hardest for me to pick up, but I also see it's value in it being a lightweight framework. At the end of the day, I think which technologies you use should be determined on a project by project basis. 

You can find the code for the weather app on my **[GitHub here]**.

To view the app in action, **[click here]**.


[here for a list]: https://docs.angularjs.org/api/ng/directive
[AngularJS: Factory vs Service vs Provider]: https://toddmotto.com/factory-versus-service
[click here]: http://bit.ly/2cdfEYU
[GitHub here]: https://github.com/yctercero/angular-weather-app
[found here]: https://yctercero.github.io/examining-js-frameworks-libraries-react/