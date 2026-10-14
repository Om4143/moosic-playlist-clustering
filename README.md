# Moosic: Can a machine curate our playlists?

Moosic is a small music startup whose experts make every playlist by hand. To grow, they want to know if machine
learning can help. I grouped **5,113 Spotify songs into 34 playlists** with K-Means clustering and posted them to Spotify.

**Answer:** yes, as a helper. It makes good first drafts very fast, but an expert still needs to check the result.

📊 **Presentation:** [Mossic_Presentation.pdf](presentation/Mossic_Presentation.pdf)

## The task

- Each playlist should have **50 to 250 songs**, so between 20 and 100 playlists for 5,000 songs.
- The songs in a playlist should **belong together**.

## How I built it

```
Clean -> 9 audio features -> PowerTransformer -> PCA (95%) -> K-Means (k = 34)
```

| Step | What I did | Why |
|---|---|---|
| Clean | Removed duplicates and one broken row: 5,235 -> 5,113 songs | Bad or repeated data gives bad playlists. |
| Features | Kept 9 that describe the sound. Dropped `key`, `mode`, `time_signature` and `duration_ms` | The model only learns from what we give it, so we keep what describes how a song sounds. |
| Scale | PowerTransformer | K-Means uses distance, so all features must be on the same scale or the biggest numbers decide everything. |
| PCA | 7 of 9 components, 95% of the information | It merges features that overlap and removes noise, while keeping almost all the information. |
| Number of playlists | Tested 20 to 100 and chose **34** | K-Means needs the number of groups in advance, and it must follow the 50-250 songs rule. |
| K-Means | Grouped similar songs into playlists | It's simple, fast and works with millions of songs. |

## Results

All 34 playlists have 72 to 223 songs. Each one gets a description from the features that stand out.

| Playlist | Description | Songs | Note |
|---|---|---|---|
| [28](https://open.spotify.com/playlist/5hic48mCXPYrUvkNuLINCL?si=yLwlu-yxQFOMIkU5SICTIA) | Quiet, calm, acoustic | 203 | Best example: Bach, Brahms, Chopin, Einaudi |
| [23](https://open.spotify.com/playlist/3slyNcyC039NdnwNQVL9rS?si=npHBjEKxQ7OLsj36qwnIww) | Happy | 223 | Limit: Katy Perry and ABBA next to Metallica and Nirvana |
| [4](https://open.spotify.com/playlist/69BU2XVBpuPiJp5su3GAtM?si=h_BVGoZcRxSGae64jTeLyw) | Spoken / rap, danceable, happy | 194 | 50 Cent, Dr. Dre, Usher |

The full list is in [`results/moosic_playlist_summary.csv`](results/moosic_playlist_summary.csv).

## Notebooks

| Notebook | What it does |
|---|---|
| [01_scaling](notebooks/01_scaling.ipynb) | Clean the data, compare 5 scalers |
| [02_nclusters](notebooks/02_nclusters.ipynb) | Choose the number of playlists (elbow, silhouette, 50-250 rule) |
| [03_pca](notebooks/03_pca.ipynb) | Reduce the features with PCA |
| [04_final_analysis](notebooks/04_final_analysis.ipynb) | Build and describe the 34 playlists |
| [05_post_to_spotify](notebooks/05_post_to_spotify.ipynb) | Post the playlists to Spotify |

## What I learned

- Spotify's features capture how a song **feels** (energy, mood, speed), but not its **style**.
  That's why pop and metal ended up together in playlist 23.
- K-Means is fast and simple, but each song fits only one playlist and we must choose the number of playlists ourselves.

## Project structure

```
├── data/
│   ├── 3_spotify_5000_songs.csv     # raw Spotify data
│   └── songs_clean.csv              # clean data (made by notebook 01)
├── notebooks/                       # run in order: 01 -> 05
├── results/
│   ├── moosic_playlists.csv         # one row per song, with its playlist
│   └── moosic_playlist_summary.csv  # one row per playlist
├── presentation/
│   └── Mossic_Presentation.pdf
└── .env.example                     # template for your Spotify keys
```

For notebook 05, copy `.env.example` to `.env` and add your Spotify keys (a Premium account is needed).

**Tools:** Python · pandas · scikit-learn · matplotlib · seaborn · Spotipy
