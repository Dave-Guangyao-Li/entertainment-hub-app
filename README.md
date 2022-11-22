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
    const [selectedGenres, setSelectedGenres] = useState([]);
    const [genres, setGenres] = useState([]);
    const genreForURL = useGenre(selectedGenres); // custom hook to get genre id for url

    const fetchMovies = async () => {
        const { data } = await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${process.env.API_KEY}&language=en-US&sort_by=popularity.desc&include_adult=false&include_video=false&page=${page}&with_genres=${genreforURL}`);
        
        setMovies(data.results);
        setNumofPages(data.total_pages); // 500 pages
      };
    
    useEffect(() => {
      fetchMovies();
    }, [page, genreForURL]);

    return (
        <div>
            <span className="pageTitle">Movies</span>
            <Genres
                type="movie"
                selectedGenres={selectedGenres}
                setSelectedGenres={setSelectedGenres}
                genres={genres}
                setGenres={setGenres}
                setPage={setPage}
            />
            <div className="trending">
                {content && content.map((c) => (
                    <SingleContent
                        key={c.id}
                        id={c.id}
                        poster={c.poster_path}
                        title={c.title || c.name}
                        date={c.first_air_date || c.release_date}
                        media_type="movie"
                        vote_average={c.vote_average}
                    />
                ))}
            </div>
            {numofPages > 1 && ( // if there is more than one page show the pagination
                <CustomPagination setPage={setPage} numOfPages={numofPages} />
            )}
        </div>
    )
    )

}
```

## useGenres custom hook
```javascript
    // create custom hook to generate genre codes
    // create hooks folder and useGenre.js
    const useGenres = (selectedGenres) => {
        if(selectedGenres.length < 1) return "";
        const GenreIds = selectedGenres.map((g) => g.id); // extrac ids from selected genres
        return GenreIds.reduce((acc, curr) => acc + "," + curr ); // join ids string with comma: 1,2,3,4
    }

      useEffect(() => {
        fetchMovies();
      }, [page]);

      export default useGenres;
```

## Series component
* similar to Movies component
* type = tv

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


## genre component
* genre tags for each movie (selected and unselected)
    * handleAdd() and handleRemove() to add and remove genre id from selectedGenres array
* select genre will update the page as well (setPage)
* map the genre name into Chip component (of Material UI)
```javascript
// in Movie.js
const [selectedGenres, setSelectedGenres] = useState([]);
const [genres, setGenres] = useState([]);

//in Genre.js
const Genres = ({
    selectedGenres,
    setSelectedGenres,
    genres,
    setGenres,
    type,
    setPage
}) => {
    // fetch genres from API /genre/${type}/list
    const async fetchGenres = async () => {
        const { data } = await axios.get(`https://api.themoviedb.org/3/genre/${type}/list?api_key=${process.env.API_KEY}&language=en-US`);

        
        setGenres(data.genres);
    }

    // add genre id to selectedGenres array
    const handleAdd = (genre) => {
        setSelectedGenres([...selectedGenres, genre]);
        setGenres(genres.filter((g) => g.id !== genre.id));
        setPage(1);
    }

    // remove genre from selectedGenres and add it back to genres array
    const handleRemove = (genre) => {
        setSelectedGenres(selectedGenres.filter((selected) => selected.id !== genre.id));
        setGenres([...genres, genre]);
        setPage(1);
    }

    
    useEffect(() => {
        fetchGenres();
        return () => {
            setGenres({}); // when changing the page, unmount the genre component and remount it
        }
    }, []);

    return (
        <div style={{padding: "6px 0"}}>
            {genres && genres.map((genre) => (
                <Chip
                    label={genre.name}
                    style={{margin: 2}}
                    clickable
                    size="small"
                    key={genre.id}
                    color={selectedGenres.includes(genre.id) ? "primary" : "default"}  
                    onClick={() => handleAdd(genre)}
                    onDelete={() => handleRemove(genre)}
                />
            ))}
        </div>
    )
}
```

## Search component
* search bar text input
* use TextField, SearchIcon, Tabs component from Material UI
* show trending movies or series when no search input

```javascript
import { TextField, createMuiTheme } from '@material-ui/core';
import useState from 'react';
import SearchIcon from '@material-ui/icons/Search';
import Tabs from '@material-ui/core/Tabs';

const Search = ()=>{
    const [type, setType] = useState(0);
    const [page, setPage] = useState(1);
    const [searchText, setSearchText] = useState(""); // search query
    const [content, setContent] = useState();
    const [numOfPages, setNumOfPages] = useState();

    const darkTheme = createMuiTheme({
        palette: {
            type: 'dark',
            primary: {
                main: '#fff',
            },
        },
    });

    const fetchSearch = async () => { // fetch search results from API /search
        // type ===1 means tv
        const { data } = await axios.get(`
        https://api.themoviedb.org/3/search/${type ? "tv" : "movie"}
        ?api_key=${process.env.API_KEY}
            &language=en-US
            &query=${searchText}
            &page=${page}
            &include_adult=false
        `);

        setContent(data.results);
        setNumOfPages(data.total_pages);
    } 

    useEffect(() => {
        window.scroll(0,0); // scroll to top
        fetchSearch();
    }, [type, page]);

    return (
        <div>
        <ThemeProvider theme={darkTheme} >
            <div style={{display:"flex", margin:"15px, 0"}}>
            <TextField // search bar
                    style={{flex: 1}}
                    className="searchBox"
                    label="Search"
                    variant="filled"
                    onChange={(e) => setSearchText(e.target.value)}
            />
            
            <Button variant="contained" style={{marginLeft:10}} onClick={fetchSearch}>
                <SearchIcon />
            </Button>
            
            <Tabs value={type} indicatorColor="primary" textColor="primary"
            onChange={(event, newValue)=>{
                setType(newValue);
                setPage(1);
            }}>
                <Tab style={{width: "50%"}} label="Search Movies" />
                <Tab style={{width: "50%"}} label="Search Series" />
            </Tabs>
        </ThemeProvider>


        <div className="trending">
            {/*copy and paste trending, according to type show different results*/}
            {searchText && 
            !content && 
            (type ? <h2>No Series found</h2> : <h2>No Movies found</h2>)}
        </div>
        {numOfPages > 1 && (
            <CustomPagination setPage={setPage} numOfPages={numOfPages} />
        )}

            
        </div>
    )
}
```

## ContentModal component (details of each movie/series)
* use material ui modal as ContentModal component
* landscape and portrait image both available
* show title, tagline, overview, release date, watch trailer button
```javascript

const [content, setContent] = useState();
const [video, setVideo] = useState();

const fetchData = async () => {
    // fetch movie/series details from API /movie/{movie_id} or /tv/{tv_id}
    const { data } = await axios.get(`
    https://api.themoviedb.org/3/${type}/${id}?api_key=${process.env.API_KEY}&language=en-US
    `);

    setContent(data);
}

const fetchVideo = async () => {
    // fetch movie/series video from API /movie/{movie_id}/videos or /tv/{tv_id}/videos
    const { data } = await axios.get(`
    https://api.themoviedb.org/3/${type}/${id}/videos?api_key=${process.env.API_KEY}&language=en-US
    `);

    setVideo(data.results[0]?.key);
}

useEffect(() => {
    fetchData();
    fetchVideo();
}, []);
```

## Carousel component
* react-alice-carousel library
* Carousel.js folder under ContentModal folder
* show casts of each movie/series

```javascript
    <div>
    <Carousel id={id} media_type={media_type}>
    </div>
```
```javascript
// copy and paste from react-alice-carousel github usage snippet
// Carousel.js
const [credits, setCredits] = useState();

const items = credits?.map((c) => (
    // image and name of each cast
    <div className="carouselItem">
        <img
            src={c.profile_path ? `${img_300}/${c.profile_path}` : unavailable}
            alt={c?.name}
            className="carouselItem__img"
        />
        <b className="carouselItem__txt">{c?.name}</b>
    </div>
));

const fetchCredits = async () => {
    // fetch movie/series credits from API /movie/{movie_id}/credits or /tv/{tv_id}/credits
    const { data } = await axios.get(`
    https://api.themoviedb.org/3/${media_type}/${id}/credits?api_key=${process.env.API_KEY}&language=en-US
    `);

    setCredicts(data.cast);
}

useEffect(() => {
    fetchCredits();
}, []);

const responsive = {
    0: {
        items: 3,
    },
    512: {
        items: 5,
    },
    1024: {
        items: 7,
    },
};

return (
    <AliceCarousel
        autoPlay
        infinite
        autoPlayInterval="3000"
        disableDotsControls
        disableButtonsControls
        mouseTracking
        items={items}
    />
);
```
```