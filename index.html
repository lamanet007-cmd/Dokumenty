const { addonBuilder, serveHTTP } = require('stremio-addon-sdk');

const TMDB_KEY = process.env.TMDB_API_KEY;
if (!TMDB_KEY) { console.error('Chybí TMDB_API_KEY'); process.exit(1); }

// CZ_MODE: "translation" = má české info v TMDB (výchozí)
//          "original"    = pouze původně české tituly (cs)
//          "all"         = bez filtru
const CZ_MODE = process.env.CZ_MODE || 'translation';
const LIMIT = +process.env.LIMIT || 500;        // horní strop na typ
const MIN_VOTES = +process.env.MIN_VOTES || 30; // minimum hlasů v TMDB
const MAX_PAGES = +process.env.MAX_PAGES || 200;
const REFRESH_MS = 24 * 3600 * 1000;

const cache = { movie: [], series: [] };

async function tmdb(path, params = {}) {
  const qs = new URLSearchParams({ api_key: TMDB_KEY, ...params });
  for (let i = 0; i < 3; i++) {
    const r = await fetch(`https://api.themoviedb.org/3${path}?${qs}`);
    if (r.status === 429) { await new Promise(s => setTimeout(s, 1500)); continue; }
    if (!r.ok) return null;
    return r.json();
  }
  return null;
}

function passesCzech(d) {
  if (CZ_MODE === 'all') return true;
  if (CZ_MODE === 'original') return d.original_language === 'cs';
  const tr = d.translations?.translations || [];
  return tr.some(t => t.iso_639_1 === 'cs' && (t.data?.overview || '').trim());
}

async function build(kind) {
  const isMovie = kind === 'movie';
  const out = [];
  const seen = new Set();
  for (let page = 1; page <= MAX_PAGES && out.length < LIMIT; page++) {
    const res = await tmdb(`/discover/${isMovie ? 'movie' : 'tv'}`, {
      with_genres: 99,
      sort_by: 'vote_average.desc',
      'vote_count.gte': MIN_VOTES,
      page
    });
    if (!res?.results?.length) break;
    const details = await Promise.all(res.results.map(r =>
      tmdb(`/${isMovie ? 'movie' : 'tv'}/${r.id}`, {
        append_to_response: 'translations,external_ids',
        language: 'cs-CZ'
      })
    ));
    for (const d of details) {
      if (!d || !passesCzech(d)) continue;
      const imdb = d.imdb_id || d.external_ids?.imdb_id;
      if (!imdb || seen.has(imdb)) continue;
      seen.add(imdb);
      out.push({
        id: imdb,
        type: isMovie ? 'movie' : 'series',
        name: d.title || d.name,
        poster: d.poster_path ? `https://image.tmdb.org/t/p/w500${d.poster_path}` : undefined,
        background: d.backdrop_path ? `https://image.tmdb.org/t/p/w1280${d.backdrop_path}` : undefined,
        description: d.overview,
        releaseInfo: (d.release_date || d.first_air_date || '').slice(0, 4),
        imdbRating: d.vote_average ? d.vote_average.toFixed(1) : undefined,
        genres: ['Dokument']
      });
    }
    if (page >= res.total_pages) break;
  }
  return out;
}

async function refresh() {
  for (const [kind, key] of [['movie', 'movie'], ['tv', 'series']]) {
    try {
      const list = await build(kind);
      if (list.length) cache[key] = list;
      console.log(`${key}: ${list.length} titulů (mode=${CZ_MODE})`);
    } catch (e) { console.error('Chyba při buildu', key, e); }
  }
}

const manifest = {
  id: 'community.best.docs.cz',
  version: '1.0.0',
  name: 'Nejlepší dokumenty (TMDB, CZ)',
  description: 'Nejlépe hodnocené dokumenty dle TMDB s českou lokalizací.',
  resources: ['catalog'],
  types: ['movie', 'series'],
  idPrefixes: ['tt'],
  catalogs: [
    { type: 'movie', id: 'docs-movies-cz', name: 'Nejlepší dokumentární filmy', extra: [{ name: 'skip' }] },
    { type: 'series', id: 'docs-series-cz', name: 'Nejlepší dokumentární seriály', extra: [{ name: 'skip' }] }
  ]
};

const builder = new addonBuilder(manifest);
builder.defineCatalogHandler(({ type, extra }) => {
  const skip = +(extra?.skip || 0);
  return Promise.resolve({ metas: (cache[type] || []).slice(skip, skip + 100) });
});

refresh();
setInterval(refresh, REFRESH_MS);

serveHTTP(builder.getInterface(), { port: process.env.PORT || 7000 });
