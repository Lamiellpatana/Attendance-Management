# Railway Mobile Deploy Guide

Goal: mapatakbo ang AttendEase online para mabuksan ng mobile users sa browser gamit ang Railway URL.

## Ano ang Na-ready sa Project

- `Dockerfile` para automatic ma-build ang Java app sa Railway.
- `.dockerignore` para hindi maisama ang local secrets at build outputs.
- `.env.example` para mabilis ma-copy ang Railway variables.
- `App.java` reads Railway `PORT` and MySQL environment variables.
- Kapag empty ang Railway MySQL database, auto-import ang `database/schema.sql` at `database/seed.sql`.

## Steps sa Railway

1. I-upload ang project sa GitHub.
   - Siguraduhin na kasama sa repo ang `Dockerfile`, `.dockerignore`, `.env.example`, `src`, `web`, `lib`, `database`, at `config.properties`.
   - Huwag i-upload ang `config.local.properties`.

2. Gumawa ng Railway project.
   - Go to Railway.
   - Click `New Project`.
   - Piliin `Deploy from GitHub repo`.
   - Piliin ang AttendanceManagement repository.

3. Magdagdag ng MySQL sa same Railway project.
   - Sa Project Canvas, click `+ New`.
   - Piliin `Database`.
   - Piliin `MySQL`.

4. I-set ang variables sa app service.
   - Buksan ang app service, hindi yung MySQL service.
   - Go to `Variables`.
   - Open `RAW Editor`.
   - Paste ito:

```env
DB_HOST=${{MySQL.MYSQLHOST}}
DB_PORT=${{MySQL.MYSQLPORT}}
DB_NAME=${{MySQL.MYSQLDATABASE}}
DB_USER=${{MySQL.MYSQLUSER}}
DB_PASSWORD=${{MySQL.MYSQLPASSWORD}}
DB_SSL=false
DB_TIMEZONE=UTC

MAIL_ENABLED=false
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_FROM_NAME=AttendEase
MAIL_FROM=
MAIL_PASSWORD=
```

Kung iba ang pangalan ng MySQL service mo, palitan ang `MySQL` sa variables. Example: kung service name ay `attendance-db`, gamitin `${{attendance-db.MYSQLHOST}}`.

5. Deploy.
   - Click `Deploy` or `Redeploy`.
   - Sa logs, dapat makita:

```text
[DB] Connected successfully!
[SERVER] Running on 0.0.0.0:<port>
```

6. Generate public domain.
   - Buksan ang app service.
   - Go to `Settings`.
   - Hanapin `Networking` / `Public Networking`.
   - Click `Generate Domain`.
   - Railway will give a URL like:

```text
https://your-app-name.up.railway.app
```

7. Buksan sa phone.
   - Open Chrome/Safari sa mobile.
   - Punta sa Railway URL.
   - Login gamit default seed account:

```text
admin / admin123
teacher1 / admin123
student1 / admin123
```

8. Optional: gawing parang mobile app.
   - Sa mobile browser, open menu.
   - Piliin `Add to Home Screen`.
   - Lalabas siya parang app icon.

## Important Notes

- Railway public URL uses HTTPS, kaya mas malaki ang chance na gumana ang camera QR scanner at geo-location kaysa sa local `http://192.168.x.x:8080`.
- Kung gagamit ng OTP email, set `MAIL_ENABLED=true`, `MAIL_FROM`, at Gmail App Password sa `MAIL_PASSWORD`.
- Default accounts are for testing. Palitan ang passwords pagkatapos ma-deploy.

## References

- Railway Dockerfile docs: https://docs.railway.com/deploy/dockerfiles
- Railway MySQL variables: https://docs.railway.com/guides/mysql
- Railway public networking and `PORT`: https://docs.railway.com/public-networking
- Railway variables: https://docs.railway.com/variables
