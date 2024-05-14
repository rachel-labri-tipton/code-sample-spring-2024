# Zoom Feature for Quartz Solar app with Recharts Library (React)

A feature that I worked on at **Open Climate Fix** was on the **Quartz Solar**. The app provides data visualization for solar generation forecasts in the UK.

To contextualize this discussion, here's a screenshot of the app: 

![](src/images/Screenshot%202024-05-13%20at%2017.07.22.png)

## Goal 
Enhance the solar generation forecast chart by letting a user zoom in on the data they are viewing. 

**Timeframe**

The **Zoom Feature** went through several iterations from December 2023 - March 2024. It was occasionally put on hold for other work but is now in production. 

## Feature Brief 

Our app had to do the following:

* **Allow a user to visually select data** 
* **Zoom in on selected data** - They would be able to zoom in on one chart and set the zoom level for four charts in the app.
* **Allow user to zoom out**

## **Technologies** Used

- **[React](https://reactjs.org/)** 
- **[Recharts Library](https://recharts.org/en-US)**
- **[Tailwind](https://tailwindcss.com/)** - This is the CSS library for the app.
- Git/GitHub
- VSCode
- TypeScript 

### This is what the feature looks like in production: 

![](src/images/May-13-2024%2018-06-12.gif)


## Planning 
*The **Recharts Library** doesn't have a built-in Zoom Feature, so I researched other chart libraries, such as **Plotly**, which has a JavaScript version. 
*I brought my research to the team, and we decided to use the tools in the Recharts Library to construct the feature, but it would use more in-depth knowledge of the library. 

## Development
Once the team decided to go with Recharts, I researched how others had figured out how to 

<details>
<summary>Reference Area</summary>

</details>

### Reference Area

### Mouse Events

### Resetting the chart to its default



* As a React Library, Recharts comes with a set of out-of-the-box functions, so helping a user zoom in on the chart was a matter of . 

* To better illustrate this point, below I've included the Calendar section of our CalendarPage React component:

```
<Calendar  // This is the calendar library. The lines below determine how our customized library works

// FUNCTIONS These pass the above defined functions from the CalendarPage.js component into the Calendar library functionality
onChange={onChange}
onClickDay={onClickDay}
value={value}
onActiveStartDateChange={onActiveStartDateChange}

// SETTINGS Below are simply parameters affecting how the calendar 
is displayed minDetail="month" // this means users can only see a month view (change to year to have year and month options)

maxDate={new Date()} // this stops users selecting future dates
tileContent={decideImage}
selectRange={true} // this allows users to select a range of dates - 
we will use this to show the pictures from all of these dates defaultView="month"
showNeighboringMonth={false} // this ensures we only show dates from the current month
className={['react-calendar']}
tileClassName="tile"

```

The piece of our CalendarPage component that I worked on was onClickDay(). This is the function called when a specific day of the calendar is clicked. This function allows a user to move from the calendar to the DatePage component that renders the NASA APOD along with an image description. And this required us to transform the target value date that the calendar immediately gives into a string that could be passed as a parameter using the React useParams method into our API call and as an ":id" in the http request, which required researching and finding the toISOString() method. For example, the e.target.value date automatically provided by React Calendar is, for example, "Wed Apr 07 2021 00:00:00 GMT+0000" and we needed something like 2021-04-07 (YYYY-MM-DD). I have to credit my teammate Julie Park with figuring this one out and finding the toISOString() method on StackOverflow. 

*Here's the commented code for onClickDay()*: 
```
  function onClickDay(value) {
    // points to the DatePage component
    const calendarDate = new Date(
      value.getTime() -
      value.getTimezoneOffset() * 60000
    ).toISOString().split('T')[0] // adjust the clicked date to offset issues with British Summer Time and format it into YYYY-MM-DD
    const dateToString = calendarDate.toString() // convert the date into string
    // takes a user to a corresponding date page
    navigate(`/datepage/${dateToString}`)
  }
  ```

### DatePage
* With the correctly formatted string, we are able to use it in the path for the Route that renders our DatePage component. 

```
 <Route path="/datepage/:date" element={<DatePage />}/>

 ```

The correctly formatted date is then passed through the DatePage component as a parameter using the React useParams() method that is then passed through the API request as seen below:  

```
function DatePage() {
  const { date } = useParams();
  const [apod, setApod] = React.useState(undefined);
//apod = Astronomy Picture of the Day (abbreviation used by the NASA APOD API)
  React.useEffect(() => {
    async function fetchApod() {
      const resp = await fetch(`https://api.nasa.gov/planetary/apod?date=${date}&api_key=ZNZOJj0Nq1kjV9IBBHp5qNWaAfThwOh4Kn98vhuY`);
      const data = await resp.json();
      setApod(data);
      console.log(resp)
    }
    fetchApod();
  }, [date]);

  ```

In addition to working on the two above functions, I also took the lead on styling and deployment for our project. This was my first time working with a styling library, so I watched a Traversy Media tutorial on the basics and read through the documentation.  The styling of the DatePage component is the result of me working with Bulma cards and columns. 

## Deployment 

Since we had been informed that deployment could take some time, I wanted to make sure I understood how it worked, so I jumped into working on it first thing the final day of the project. Also, as we had used a repository on my GitHub account, it made sense that I took the lead on deployment. 

## Wins
There were a lot of firsts with this project, and our group handled them all really well. Here are some of our wins:
- First time implementing a React Calendar library. 
- First time manipulating JavaScript dates.
- First time installing and working with a styling library (Bulma). 
- First time researching an API  that was relatively easy to work with, allowed unlimited requests, and met the needs of our project. 

## Challenges
During our project we had a couple of obstacles to overcome and these can be broken into Technical and Team Challenges 

### Technical Challenges

* Getting the React Calendar library and the NASA APOD API to play nicely together was tough. In the beginning, the NASA API images wouldn't load on the calendar. We overcame this by adding a 'loading' image, which would show while the NASA image URLs were still loading. This simple check ensured that as soon as the data was fetched, the images would show, as React would reload the page with the new data. This is still an ongoing issue that to load the data for a whole month, as we do for the calendar page, takes a noticeable time. Although we could not speed this up due to API limitations, our loading images minimise the impact. The user can see something while they wait.

* A more technical issue is the 'back to calendar' button from any given date page to direct the user to the calendar month the date was from. Instead, it always directs to the real-life current month. This is something we ideated around, including changing the URL of the calendar to include the month shown, but essentially didn't have time to implement the complexity it required.

* One thing we would do differently next time is to write more pseudo-code at the beginning of the project. We feel this would help to structure our process more and help us look at problems from the same angle. When working on a project alone, a lot of the pseudo-code can be in your head, but with other people it's much better to articulate what data is being passed from one component to another, especially when we each ended up working separately on those components. 



### Team Challenges 

* Another hurdle we faced was working together as a team. Since this was our first pair-coding exercise, we had to figure out how to split up the work. It would have made sense to use Trello or another planning tool, but I think the scale of the project seemed manageable enough without that. If I could do this project over, I'd have pushed for implementing planning software. 

* Working together took a lot more communication and patience figuring out how other team members communicated best. Since we were on Zoom, I preferred to speak up when I had a question while it seemed like other team members were more responsive if I just texted via Slack. Not understanding how other people communicated led to moments where we'd be working on the same sections of code not really knowing what the other person was doing. About the halfway point through the project we had a discussion as a team and found ways to not do this. One tactic which worked really well was splitting out the work between us, and agreeing to check in 30 minutes later for a quick update. One time this worked especially well, was when we were choosing which calendar API to use, and we investigated one of these each to report back.
We also swapped tasks when stuck on something, which helped to get fresh eyes on the code.

* At times there were only 2 clear technical tasks to work on between the 3 of us. This is why I went ahead and spent time learning Bulma and working with a styling library. 

    
## Key learnings 

* How to implement a React library, such as the React Calendar library. 
* Using resources like StackOverflow to figure out working with dates in JavaScript. 
* How to use a styling library like Bulma. I found in subsequent projects that I enjoyed working through a tutorial  
* Project deployment on Netlify.
* Working in a group of three can be difficult, but being clear on how everyone communicates from the start can be the best plan of action. 

## If we'd had more time...

#### If we'd had more time, we would have liked to have done the following: 

- Implement functionality on the "Back to Calendar" button taking the user from the page with the big image back to the month they were on in the calendar as opposed to the current month. 
- Disable the next button when APOD data is unavailable. (e.g. dates in the future)
- Ensure the footer stays at the bottom of the screen on each page, and work a bit more on the styling of the calendar itself. This would require more time with the React Calendar library.



