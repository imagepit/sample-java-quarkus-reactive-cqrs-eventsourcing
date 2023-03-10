
```sql
insert into 
events (
  aggregate_id,
  aggregate_type,
  event_type,
  data, 
  metadata,
  version,
  timestamp
) 
values (
  $1, $2, $3, $4, $5, $6, now()
)
```

load events

```sql
select 
  event_id,
  aggregate_id,
  aggregate_type,
  event_type,
  data,
  metadata,
  version,
  timestamp
from 
  events e 
where 
  e.aggregate_id = $1
and 
  e.version > $2 
order by 
  e.version 
asc
```

HANDLE_CONCURRENCY_QUERY

```sql
select 
  aggregate_id 
from 
  events e 
where 
  e.aggregate_id = $1 
limit 1 
for update
```

SAVE_SNAPSHOT_QUERY

```sql
insert into 
  snapshots(
    aggregate_id,
    aggregate_type,
    data,
    metadata,
    version,
    timestamp
  )
values ($1, $2, $3, $4, $5, now()) 
on conflict (aggregate_id) do 
update set 
  data = $3,
  version = $5,
  timestamp = now()
```