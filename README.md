## Prisma-Schema-Date-Time

```schema
generator client {
  provider = "prisma-client-js"
  output   = "../app/generated/prisma"
}

datasource db {
  provider = "postgresql"
}

model User {
  id Int @id @default(autoincrement())

  col1 DateTime
  col2 DateTime @db.Date
  col3 DateTime @db.Time(3)
  col4 DateTime @db.Timestamp(6)
  col5 DateTime @db.Timestamptz(6)
  col6 DateTime @db.Timetz(6)
}
```
---

```bash
npx prisma validate
```
---
