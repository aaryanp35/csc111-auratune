# AuraTune

A desktop music recommender that matches songs to your mood by walking a decision tree built from Spotify audio features.

Built for CSC111 (Data Structures and Analysis) at the University of Toronto, together with a CSC111 teammate.

## What and why

Most recommenders are black boxes. AuraTune goes the other way: you pick a genre, then say whether you want **low**, **medium**, or **high** danceability, energy, instrumentalness, valence (musical positivity), and loudness. The app walks a custom tree data structure that answers your choices, and returns songs that fit that exact "vibe". Every recommendation can be traced back to the choices that produced it.

## Features

- Browse 113 genres from a dataset of roughly 74,500 tracks with Spotify audio features
- Choose a low, medium, or high level for five attributes: danceability, energy, instrumentalness, valence, loudness
- Get up to five matching songs with artist names
- Simple step-by-step Tkinter interface with a progress indicator ("Step 3 of 6")

## How it works

1. **Load data.** `main.py` reads `song_data.csv` with pandas and collects the list of genres.
2. **Bucket the features.** When you choose a genre, `graph.py` computes the 33rd and 66th percentiles of each of the five attributes within that genre. Those cutoffs turn each raw number into LOW, MEDIUM, or HIGH. Using per-genre percentiles means "high energy" is relative to the genre, so a loud metal track and a loud folk track are each compared with their own peers.
3. **Build the decision tree.** Each song is inserted into a `SongDecisionTree` that has one level per attribute (five levels deep). The path from root to leaf is the song's LOW/MEDIUM/HIGH profile, and the leaf holds all songs sharing that profile.
4. **Search.** Your five answers become a path through the tree. Lookup follows one branch per level, so it never scans the whole genre. The GUI shows the first few songs found at that leaf.

## Tech stack

- Python 3.12 or newer
- pandas and NumPy for data loading and percentile thresholds
- Tkinter for the GUI
- A custom tree (`SongDecisionTree`) and `Enum`-based `CategoryLevel`

## Quick start

```bash
git clone https://github.com/aaryanp35/csc111-auratune.git
cd csc111-auratune
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python main.py
```

Notes:
- Python 3.12+ is required (the GUI uses f-string syntax that older versions reject).
- Tkinter ships with most Python installers. On some Linux distributions you may need to install it separately, for example `sudo apt install python3-tk`.
- `song_data.csv` is about 13 MB, so the first launch takes a moment to load.

## Project structure

```
.
├── main.py           # Entry point: loads the CSV and launches the GUI
├── gui.py            # GUIApp (Tkinter): home, info, selection, and results pages
├── graph.py          # Song class and SongDecisionTree, plus bucketing helpers
├── const.py          # CategoryLevel enum, tracked attributes, dataset path
├── api.py            # Optional Spotify Web API client (not used by the app)
├── song_data.csv     # Track metadata and audio features
└── requirements.txt
```

`api.py` is a small client for the Spotify Web API (client-credentials auth, genre search, artist lookup). The app itself runs entirely from the bundled CSV. Spotify has since removed the audio-features endpoint that part of the client relied on, which is why the project ships a static dataset. Running it directly would also need `requests` and `python-dotenv` (not in `requirements.txt`) plus your own Spotify `CLIENT_ID` and `CLIENT_SECRET`.

## Highlights and what I learned

- Designed a tree where depth corresponds to a feature, which turns a multi-attribute filter into a short path lookup
- Chose percentile-based thresholds computed per genre, so categories stay balanced and meaningful within each genre
- Practised separating data logic (`graph.py`), constants (`const.py`), and presentation (`gui.py`)
- Worked with a teammate on a shared codebase and a real-world messy dataset

### Known limitations and ideas for next steps

- A genre-and-profile combination can have no matches, in which case the results page is empty
- Results come back in dataset order, not ranked; sorting by popularity is a natural next step
- Recommendations are exact-match on all five levels, so a "nearest neighbour" fallback would make the results more forgiving

## Author and credits

**Aaryan Patel**, 3rd-year Economics (Data Analytics) student at the University of Toronto, with minors in Computer Science and Statistics.

- Email: aaryanp35@gmail.com
- LinkedIn: https://www.linkedin.com/in/aaryanp35
- Portfolio: https://aaryanp35.vercel.app

Built with a CSC111 teammate. Course scaffolding and file headers are copyright the CSC111 Teaching Team, University of Toronto.
