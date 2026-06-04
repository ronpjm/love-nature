# Supabase 연동 설정

이 홈페이지는 `assets/supabase-config.js`에 Supabase 정보가 들어가면 모든 기기에서 같은 다짐을 공유합니다.

## 1. Supabase 프로젝트 만들기

1. https://supabase.com 에 접속합니다.
2. 새 프로젝트를 만듭니다.
3. 프로젝트가 생성되면 `Project Settings > API`에서 아래 값을 확인합니다.
   - Project URL
   - anon public key

## 2. 테이블 만들기

Supabase의 `SQL Editor`에서 아래 SQL을 실행합니다.

```sql
create table if not exists public.commitments (
  id text primary key,
  grade integer not null check (grade between 1 and 6),
  cls integer not null check (cls between 1 and 9),
  nickname text not null check (char_length(nickname) <= 10),
  text text not null check (char_length(text) <= 60),
  kind text not null check (kind in ('leaf', 'blossom', 'berry')),
  variant integer not null check (variant between 1 and 3),
  hue integer not null,
  created_at timestamptz not null default now()
);

alter table public.commitments enable row level security;

drop policy if exists "commitments_select_all" on public.commitments;
drop policy if exists "commitments_insert_all" on public.commitments;
drop policy if exists "commitments_update_all" on public.commitments;
drop policy if exists "commitments_delete_all" on public.commitments;

create policy "commitments_select_all"
on public.commitments for select
to anon
using (true);

create policy "commitments_insert_all"
on public.commitments for insert
to anon
with check (true);

create policy "commitments_update_all"
on public.commitments for update
to anon
using (true)
with check (true);

create policy "commitments_delete_all"
on public.commitments for delete
to anon
using (true);
```

## 3. 홈페이지 설정값 넣기

`assets/supabase-config.js`를 아래처럼 바꿉니다.

```js
window.NATURE_TREE_SUPABASE = {
  url: "https://YOUR_PROJECT_ID.supabase.co",
  anonKey: "YOUR_ANON_PUBLIC_KEY",
  table: "commitments",
};
```

## 참고

이 사이트는 GitHub Pages에서 동작하는 정적 사이트라 서버 비밀키를 숨길 수 없습니다.
위 정책은 교실/행사용 간단 운영에 맞춘 설정이며, 개발자 도구를 아는 사용자는 수정/삭제 API를 호출할 수 있습니다.
강한 보안이 필요하면 Supabase Auth 또는 Edge Function을 추가해야 합니다.
