# Movie-Recommendor
My movie recommendation system site



import streamlit as st
import pickle
import pandas as pd
import requests



def fetch_poster(movie_id):
    response = requests.get(
        f'https://api.themoviedb.org/3/movie/{movie_id}?api_key=6480aba808d15e2c1a14f44618060fcc&language=en-US')
    data = response.json()
    return f"https://image.tmdb.org/t/p/w500/{data['poster_path']}"


def recommend(movie):
    try:
        movie_index = movies[movies['title'] == movie].index[0]
        distances = similarity[movie_index]
        movies_list = sorted(list(enumerate(distances)), reverse=True, key=lambda x: x[1])[1:6]

        recommended_movies = []
        recommended_movies_posters = []
        for i in movies_list:
            movie_id = movies.iloc[i[0]]['id']
            recommended_movies.append(movies.iloc[i[0]]['title'])
            # fetch poster from API
            recommended_movies_posters.append(fetch_poster(movie_id))

        print(f"Recommended movies: {recommended_movies}")  # Debugging statement
        print(f"Recommended posters: {recommended_movies_posters}")  # Debugging statement

        return recommended_movies, recommended_movies_posters
    except Exception as e:
        print(f"Error in recommend function: {e}")
        return None, None


movies_dict = pickle.load(open('movie_dict.pkl', 'rb'))
movies = pd.DataFrame(movies_dict)
print(movies.columns)

similarity = pickle.load(open('similarity.pkl', 'rb'))

st.title("Movie Recommender System")

selected_movie_name = st.selectbox(
    'Select a movie to get recommendations',
    movies['title'].values
)

if st.button('Recommend'):
    names, posters = recommend(selected_movie_name)
    if names and posters:
        col1, col2, col3, col4, col5 = st.columns(5)
        with col1:
            st.text(names[0])
            st.image(posters[0])
        with col2:
            st.text(names[1])
            st.image(posters[1])
        with col3:
            st.text(names[2])
            st.image(posters[2])
        with col4:
            st.text(names[3])
            st.image(posters[3])
        with col5:
            st.text(names[4])
            st.image(posters[4])
    else:
        st.write("No recommendations available.")

