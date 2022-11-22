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
    )
}