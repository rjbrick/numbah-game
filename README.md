# Numbah

A daily numbers puzzle game inspired by the Countdown numbers round.

Six numbers. One target. How close can you get?

**[Play at numbah.game](https://numbah.game)**

---

## How to play

- You're given 6 numbers and a 3-digit target
- Use +, −, ×, ÷ to combine the numbers and reach the target
- Each number can only be used once
- You don't have to use all of them
- Division must produce whole numbers only
- Calculated results appear as new tiles you can reuse

## Scoring

| Result | Points |
|--------|--------|
| Exact | 10 |
| Within 5 | 7 |
| Within 10 | 5 |
| More than 10 away | 0 |

## Features

- Daily puzzle — same numbers and target for everyone each day
- Streak tracking
- Score synced across devices via Supabase (sign in with magic link)
- Spoiler-free share card

## Tech

Single HTML file. No framework. No build step.

- Vanilla JS / CSS
- [Supabase](https://supabase.com) for auth and score sync
- Hosted on GitHub Pages

## Development

```bash
# Clone the repo
git clone https://github.com/rjbrick/numbah.git
cd numbah

# Open locally
open index.html
```

No build step required — just open `index.html` in a browser.

## Supabase setup

If forking and self-hosting, you'll need your own Supabase project. Run this in the SQL editor:

```sql
create table numbah_scores (
  id uuid default gen_random_uuid() primary key,
  user_id uuid references auth.users(id) on delete cascade,
  played int default 0,
  streak int default 0,
  best_streak int default 0,
  exact_count int default 0,
  dist jsonb default '{"10":0,"7":0,"5":0,"0":0}',
  last_date text default null,
  updated_at timestamptz default now()
);

alter table numbah_scores add constraint numbah_scores_user_unique unique (user_id);
alter table numbah_scores enable row level security;

create policy "Users can read own scores" on numbah_scores
  for select using (auth.uid() = user_id);

create policy "Users can upsert own scores" on numbah_scores
  for insert with check (auth.uid() = user_id);

create policy "Users can update own scores" on numbah_scores
  for update using (auth.uid() = user_id);
```

Then update `SUPABASE_URL` and `SUPABASE_ANON` in `index.html`.

---

Built by [@rjbrick](https://github.com/rjbrick)
