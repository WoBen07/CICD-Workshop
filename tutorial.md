# 1. Installation von Nodejs

Mac: $ brew install node

Win: https://nodejs.org/en/download/package-manager

# 2. React Applikation erstellen

$ npx create-react-app cicd-workshop

# 3. Start der erstellten React Applikation

$ npm start

Browser: http://localhost:3000/

# 4. Github Repository anlegen

Browser: https://github.com/new

Privates Repo anlegen

# 5. Lokales git Repository anlegen und Github als Remote setzen

$ git init

$ git remote add origin https://github.com/...

$ git push -u

# 6. Eine minimale Pipeline erstellen

File: .github/workflows/build.yml

```
name: CI/CD Workshop
on:
  - push
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run test
```

# 7. Artefakte erstellen und hochladen

File erweitern: .github/workflows/build.yml

```
      - run: npm run build
      - uses: actions/upload-artifact@v4
        with:
          name: build
          path: build
```

# 8. AWS Secrets in Github hinterlegen

Github AWS Secrets hinzufügen

AWS_ACCESS_KEY_ID = AKIAZLQCADUUV2XP3VYW
AWS_SECRET_ACCESS_KEY = q3UNftqteEPSSx8azv49mCDkvGjTGRu

Github AWS Variables hinzufügen

AWS_BUCKET = mw-cicdworkshop-73a481c-$i
AWS_REGION = eu-central-1

($i ist deine Nummer)

# 9. Artifact zu AWS S3 synchronisieren

File erweitern: .github/workflows/build.yml

```
  dist:
    runs-on: ubuntu-latest
    needs:
      - build
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: build
          path: build
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ vars.AWS_REGION }}
      - run: aws s3 sync ./build/ s3://${{ vars.AWS_BUCKET }} --delete
```

URL: http://mw-cicdworkshop-73a481c-$i.s3-website.eu-central-1.amazonaws.com

# 10. Änderungen vornehmen und neu pushen
