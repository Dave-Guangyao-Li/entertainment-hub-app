# Movies and TV Series Searching App in React JS and Material UI 🔥

```
install react-router-dom
import BrowserRouter, Switch, Route, Link from react-router-dom

set up navigation between pages with react-router-dom first

```

## MainNav component
* four pages: trending, movies, tv series, search
* store history in state to use it in search page 


## Trending component  
* fetch data from moviedb API
```javascript
const [content, setContent] = useState([]);
const fetchTrending = async () => {
    const { data } = await axios.get(`https://api.themoviedb.org/3/trending/all/day?api_key=${process.env.API_KEY}`);
    
    setContent(data.results);
  };
}

useEffect(() => {
  fetchTrending();
}, []);
```
```


## Movies component

## Series component

## config.js
* store picture url

## create .env file to store api key