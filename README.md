# Final Project for Data Science and Applications
The project includes three main parts:
- Data Crawling
- Exploratory Data Analysis (EDA)
- Prediction model

# Data Crawling
Folder: [crawler](https://github.com/bathinh001/1712168/tree/main/crawler), [data](https://github.com/bathinh001/1712168/tree/main/data).

## Movie
Implementation File: [movies_crawler.ipynb](https://github.com/bathinh001/1712168/blob/main/crawler/movies_crawler.ipynb).

Output Files: [movie.csv](https://github.com/bathinh001/1712168/blob/main/data/movie.csv), [credit.csv](https://github.com/bathinh001/1712168/blob/main/data/credit.csv).

This section combines two data crawling methods:

- HTML Parsing: First, it retrieves a list of 21 genres from the top chart page, then for each genre, it collects the top movies with 10,000+ votes. This ensures we gather popular movies with more reliable user ratings. At this stage, only the movie IDs are retrieved since IMDb offers an API that allows us to extract details based on IDs.
- API: Using the `imdbpy` library, the API fetches the movie’s attributes based on the IDs collected earlier.

This results in two data sets:
- `movie.csv`: Basic information about the movie (not human-related).
- `credit.csv`: Information about the cast, director, producer, etc., for each movie.

## Rating
Implementation File: [users_crawler.ipynb](https://github.com/bathinh001/1712168/blob/main/crawler/users_crawler.ipynb).

Output File: [rating.csv](https://github.com/bathinh001/1712168/blob/main/data/rating.csv)

Since IMDb doesn’t provide an API for user data, we rely entirely on HTML parsing to gather user ratings. For each movie, we gather ratings from prolific users, as these users rate many movies, making it more likely to find their ratings across multiple movies in our dataset, which will improve recommendation accuracy.

With approximately 9,000 movies, we collected over **one million rows** (movie_id, user_id, rating). Crawling this data with a single file would take around 43 hours, so I divided it into multiple files and combined the results. Only one representative file is shown here.

# Exploratory Data Analysis (EDA)
Folder: [main](https://github.com/bathinh001/1712168/tree/main/main).

Implementation File: [EDA.ipynb](https://github.com/bathinh001/1712168/blob/main/main/EDA.ipynb).

I will create visual analyses of movie duration, ratings, genre distribution, and top movies of all time.

# Main
Folder: [main](https://github.com/bathinh001/1712168/tree/main/main).

Implementation File: [main.ipynb](https://github.com/bathinh001/1712168/blob/main/main/main.ipynb)

Prediction plays a crucial role in many recommendation systems. This section contains three prediction tasks:
- **Content-Based Filtering (Movies)**: Predict similar movies based on attributes like title, director, cast, genre, and overview. (Using TF-IDF and CountVectorizer to vectorize, then cosine similarity matrix for prediction).
- **Content-Based Filtering (User Rating Prediction)**: Predict a user’s likely rating for a movie (using SVD).
- **Collaborative Filtering**: Recommend movies for a user based on preferences of similar users (using KNN).

The first prediction is suitable if the user identity is unknown, while the last two tasks, combining top movies from both predictions, are ideal for recommending based on user preferences.

## 1. Movie Prediction Based on Attributes

Similarities between movies tend to be in genre, plot, and even the actors and directors involved.

We’ll use text from fields like title, overview, and genres in `movie.csv`, plus all fields from `credit.csv`, and join them by `movie_id`.

Vectorization: TF-IDF is used for the overview field since its important keywords will get higher scores, increasing the uniqueness of each content. `CountVectorizer` is used for credit and genre information, as we want highly recurring keywords to signify correlation.

Prediction: By concatenating the two vector matrices and calculating cosine similarity, we can identify top similar movies.

## 2. Predicting User Preferences for Movies Using Ratings Data

Here, we use SVD (Singular Value Decomposition) with Root Mean Square Error (RMSE) as the evaluation metric. Lower RMSE values indicate better performance.

The data is split 70:30, ensuring at least one row per user_id in the training set before sampling, with duplicates removed in training.

RMSE for the training set is around 1.2475, and for the test set, about 1.187—these low values suggest good prediction performance.

## 3. Predicting Similar Users and Movie Recommendations

Each user has a list of movies they have watched in certain genres. By aggregating genre data for each user, we create a 21-dimensional vector for each user, forming a matrix to find nearest neighbours using cosine similarity.

## 4. Combination
The two latter prediction tasks can be combined for a final recommendation: predicting unwatched movies for a user (Task 3), followed by predicting the user’s rating for each (Task 2). Sorting this list by rating yields the final recommendation.
