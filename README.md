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

useEffect(() => {
  fetchTrending();
}, []);
```


## Movies component
* fetch data from moviedb API /discover/movie
```javascript
const Movies = ()=>{
    const [page, setPage] = useState(1);
    const [numofPages, setNumofPages] = useState();
    const [content, setContent] = useState([]);

    const fetchMovies = async () => {
        const { data } = await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${process.env.API_KEY}&language=en-US&sort_by=popularity.desc&include_adult=false&include_video=false&page=${page}`);
        
        setMovies(data.results);
        setNumofPages(data.total_pages); // 500 pages
      };

      useEffect(() => {
        fetchMovies();
      }, []);
}
```

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
```javascript
 // CustomPagination.js
import Pagination from '@material-ui/lab/Pagination';
import { createMuiTheme, ThemeProvider } from '@material-ui/core/styles';

const darkTheme = createMuiTheme({
  palette: {
    type: 'dark',
  },
});

const CustomPagination = ({setPage, numofPages=10}) => {
    const handlePageChange = (page) => {
        setPage(page);
        window.scroll(0,0);
    }
    
  };
    return (
        <div style={{width: "100%", display: "flex", justifyContent: "center", marginTop: 10}}>
        <ThemeProvider>
            <Pagination count={numofPages} onChange={(e) => handlePageChange(e.target.textContent)} color="primary" hideNextButton hidePrevButton />
        </ThemeProvider>
        </div>
    );
```
