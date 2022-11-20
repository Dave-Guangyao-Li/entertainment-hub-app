# Movies and TV Series Searching App in React JS and Material UI 🔥

```
install react-router-dom
import BrowserRouter, Switch, Route, Link from react-router-dom
set up navigation between pages with react-router-dom first

```

## MainNav component
* four pages: trending, movies, tv series, search
* store history in state to use it in search page 

## Heading component
* add onClick to logo to go back to the top (smooth scroll)
```javascript
onClick={()=>{window.scroll(0,0)}}
```
```css
*{
    scroll-behavior: smooth;
}
```

## Trending component  
* fetch data from moviedb API
```javascript
const [content, setContent] = useState([]);
const [page, setPage] = useState(1);
const fetchTrending = async () => {
    const { data } = await axios.get(`https://api.themoviedb.org/3/trending/all/day?api_key=${process.env.API_KEY}&page=${page}`);
    
    setContent(data.results);
  };
}

useEffect(() => {
  fetchTrending();
}, []);
```


## Movies component

## Series component

## config.js
* store picture url

## create .env file to store api key

# Single content component
* pass data fetched from API to this component and display it
    * title, poster, date, media type, vote average
    * material ui badge as rating
    ```javascript
    import Badge from '@material-ui/core/Badge';
    <Badge badgeContent={vote_average} color={vote_average > 6 ? "primary" : "secondary"} />
    ```
    ```

## CustomPagination component
* under Pagination folder
