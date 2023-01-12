# News

## Introduction

- Develop a React SPA (Single Page Application) and a RESTfull API to shows breaking news and provides some functions to handle it.
- Features:
  - Sign up/Sign in/Sign out;
  - List breaking news;
  - Search for specific news;
  - Like/unlike news;
  - List liked news;
  - All features but listing breaking news must require user to be signed in.

## Back-end

### API

- Back-end requirement is: build a RESTful API that consumes an external API and add some new features.
- There are a lot of news APIs available publicly like this one: [News API](https://newsapi.org/) but you can implement whichever you want.
- Although the app needs to consume an external API, it must be wrapped by your own API. It means that if you'll use [News API](https://newsapi.org/), for example, to get news, your front-end SPA must not call it directly. Instead, front-end should call your own back-end, then the back-end will call [News API](https://newsapi.org/) on backstage.
- User authentication flow must use JWT (JSON Web Token): after signing in, user will receive a JWT, which's gonna be attached to the user on internal database. To access private features (liking news, for example), the API request must contain the user token on authorization header. Once this token is valid and user owns it, they will can access the private feature.

### Routes (features)

- Public routes (its access doesn't require user to be signed in):
  - `POST /auth/signup`:
    - Create an user account given username, password and confirming its password.
  - `POST /auth/signin`:
    - Sign user in;
    - User can sign in using its username and password;
    - Once authorized (user exists and its password matches), user will receive a token (JWT) in order to gain access to private routes.
  - `POST /auth/signout`:
    - Sign user out;
    - "Signing out" means removing user token from database.
  - `GET /news`:
    - List current breaking news;
    - News doesn't need to be stored on database, you can consume get them from the external API and then forward to the client;
    - This route also must allow user searching for specific news.
- Private routes (its access requires user to be signed in):
  - `POST /news/like`:
    - User likes a news;
    - "Liking a news" means saving a news on and associating it to the user who's doing the action.  
  - `POST /news/unlike`:
    - User unlikes a news;
    - "Unliking a news" means deleting this news from database where it is attached to the current user.  
  - `GET /news/liked`:
    - List user liked news.

### Database

- Database requirements:
  - A database must be used in order to store users and liked news;
  - You can use either a SQL or noSQL database, but I pretty recommend using a noSQL one like [MongoDB](https://www.mongodb.com/);
  - With a noSQL database, it will be easier to save liked news data.
- Database tables:
  - `users`:
    | table column | type     | description                                                               |
    | ------------ | -------- | ------------------------------------------------------------------------- |
    | id           | ObjectId | user primary key. using MongoDB, it's gonna be an auto generated ObjectId |
    | username     | varchar  | unique user access                                                        |
    | password     | varchar  | user password                                                             |
    | token        | varchar  | user token to access private routes                                       |
  - `likedNews`:
    | table column | type     | description                                                                            |
    | ------------ | -------- | -------------------------------------------------------------------------------------- |
    | id           | ObjectId | news primary key                                                                       |
    | newsData     | object   | this field will contain the news data itself, which is retrieved from the external API |
    | userId       | ObjectId | id of the user who has liked the news                                                  |

### API request/response examples

- `POST /auth/signup`:

  - Request BODY:

  ```json
  {
    "usename": "username_example",
    "password": "123456"
  }
  ```

  - Response (note that the return is the created user, but password is omitted):

  ```json
  {
    "id": 1,
    "usename": "username_example"
  }
  ```

- `POST /auth/signin`:

  - Request BODY:

  ```json
  {
    "usename": "username_example",
    "password": "123456"
  }
  ```

  - Response:

  ```json
  {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
  }
  ```

- `POST /auth/signout`:

  - Headers:

  ```json
  {
    ...
    "authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
  }
  ```

- `GET /news`:

  - Request QUERY:

  ```json
  {
    "search": "soccer"
  }
  ```

  - Response:

  ```json
  {
    "news": [
      {
        ...
      },
      {
        ...
      },
      ...
    ]
  }
  ```

- `POST /news/like`:

  - Headers:

  ```json
  {
    ...
    "authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
  }
  ```

  - Request BODY:

  ```json
  {
    "news": {
      ...
    }
  }
  ```

  - Response:

  ```json
  {
    "id": "507f191e810c19729de860ea",
    "newsData": {
      ...
    },
    "userId": 1
  }
  ```

- `POST /news/unlike`:

  - Headers:

  ```json
  {
    ...
    "authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
  }
  ```

  - Request BODY:

  ```json
  {
    "newsId": "507f191e810c19729de860ea"
  }
  ```

  - Response:

  ```json
  ```

- `GET /news/liked`:

  - Headers:

  ```json
  {
    ...
    "authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
  }
  ```

  - Response:

  ```json
  {
    "likedNews": [
      {
        "id": "507f191e810c19729de860ea",
        "newsData": {
          ...
        },
        "userId": 1
      },
      {
        "id": "00000020f51bb4362eee2a4d",
        "newsData": {
          ...
        },
        "userId": 1
      },
      ...
    ]
  }
  ```

## Front-end

### SPA

- Front-end requirement is: build a React SPA that consumes your RESTful API.

### Main components

- Header:
  - Will be used to allow user navigation by showing links to the pages;
  - When signed out, there must be the following links:
    - Home;
    - Sign up;
    - Sign in.
    <img src="./images/header-default.png">
  - After signing in, there must be the following links:
    - Home;
    - Liked news;
    - Sign out.
    <img src="./images/header-authenticated.png">
- Main:
  - List news;
  - They will be breaking ones or liked ones depending on the current page.
- Footer:
  - Link to the project GitHub repository;
  - Something else.

### Pages

- As its a SPA, each page will have the same structure (header, main and footer). You should change just the main content when navigating between pages:

<img src="./images/full-page.png">

- Main component for public pages (can be shown and accessed without needing user to be authenticated):
  - `/`:
    - Home page must consume your `GET /news` API endpoint in order to show breaking news;
    - It also must contains a searchbar, which will call the `GET /news` API endpoint again, but using its search content as a query in order to filter news;
    - By navigating through pages using the header, user can sign up/sign in to start using private features;
    - There must be a button or something on each news that allows user to like it using `POST /news/like` API endpoint. The button should be shown just if the user is signed in. Once the news was liked, the news should be unliked by hiting the button again using `POST /news/unlike` API endpoint.
  - `/signup`:
    - Pages with a form to allow user creating a new account given email, password and password confirmation;
    - You can check password and password confirmation just on client side, you don't need to check it on server side (API);
    - Once the account is created successfully, user should be redirected to the `/signin` page;
    - The API endpoint used here will be the `POST /auth/signup`.
  - `/signin`:
    - Pages with a form to allow user to sign into their account given email and password;
    - Once sign in occurs successfully, you'll be able to get the user token and redirect user to the `/` page (home);
    - User token must be saved on [local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) in order to be used as authorization header when accessing API private routes;
    - The API endpoint used here will be the `POST /auth/signin`.
- Main component for private pages (can be shown and accessed just after user is authenticated, i.e. there's a token saved on localstorage):
  - `/liked`:
    - Pages like home page, but instead of listing breaking news, it should list just user liked ones by using `GET /news/liked` API endpoint;
    - Like on home page, there must be a button or something which's gonna allow user to unlike news. After doing that, news must disappear from the list.
    - For the liked ones, you don't need to show a searchbar once our API doesn't describe a way to filtering liked ones. However, its doable, but should be implemented on API side first.

## Reference

## General

- Stack for developing this project:
  - There are a lot of stacks for building web apps, one of the most used by basic applications is the MERN stack;
  - [MERN Stack](https://www.mongodb.com/mern-stack);
- Repositories:
  - [News front-end](https://github.com/ondanieldev/news-frontend):
    - A front-end SPA for news but made with Vue and without user authentication flow.
  - [News back-end](https://github.com/ondanieldev/news-backend):
    - A back-end API for news that consumes [News API](https://newsapi.org/).
  - These repositories implements a similar app described in this guide, but they are old. Although they work well, there are some problems with them like missing authentication flow. However they can be used as reference.

### Back-end useful links

- [Official Express Docs](https://expressjs.com/)
- [RESTful APIs in NodeJS](https://www.toptal.com/nodejs/secure-rest-api-in-nodejs)
- [Using Mongoose with Express](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/mongoose)
- [Authenticate Express API using JWT](https://www.digitalocean.com/community/tutorials/authenticate-a-node-es6-api-with-json-web-tokens)
- [Using Insomnia for Testing APIs](https://konghq.com/blog/insomnia-rest)
- [Enabling CORS on Express](https://expressjs.com/en/resources/middleware/cors.html)

### Front-end useful links

- [Official React Docs](https://reactjs.org/)
- [React SPA](https://dev.to/hiteshtech/a-beginners-guide-to-create-spa-with-react-js-491c)
- [React Hooks](https://reactjs.org/docs/hooks-intro.html)
- [Consuming APIs](https://www.freecodecamp.org/news/how-to-use-axios-with-react/)
- [Public and Private Routes in React](https://ui.dev/react-router-protected-routes-authentication)
- [Local Storage in React](https://blog.logrocket.com/using-localstorage-react-hooks/)
