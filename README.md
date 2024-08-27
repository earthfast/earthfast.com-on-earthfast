### ef-on-ef

The EarthFast landing page running on EarthFast.

#### How it works

For background about how EarthFast works and it's architecture see the [docs](https://docs.earthfast.com).

There's 3 pieces at play here (in order of user)
1. `.github/workflows/generate-service-worker.yml` - generates and commits the the service worker code on a cron job if there are changes. Outputs the code to `earthfast-sw.js`
2. `build/earthfast/*` and `build/index.html` - initial HTML page that displays a loading spinner and calls for the service worker `earthfast-sw.js` in the background
3. `earthfast-sw.js` loads - calls the nodes in the decentralized network to fetch the appropriate content, do checks to make sure content wasn't tampered with etc and renders site to the user

#### How to run

 Set the environment variable `PROJECT_ID
1. Either clone or fork this repo to use as the base template. You could also copy the `.github/workflows/generate-service-worker.yml` into a new repo and build around taht
2. Set the following in your project settings
`PROJECT_ID` (Environment Variable) - EarthFast ProjectID
3. Connect to the static asset hosting service of your choice. While you can use any static asset hosting platform (Github Pages, Vercel, etc to host the assets from this repo), this is running via [Cloudflare Pages using Git](https://developers.cloudflare.com/pages/framework-guides/deploy-anything/#deploy-with-cloudflare-pages)


#### Extensions

##### Custom Loading Screen

You can customize build/index.html and the assets from the build/earthfast folder to meet your needs.

##### Adding SEO 
SEO works by having a CloudFlare Worker ([worker.js](worker.js)), which checks if the request is coming from a search engine crawler/link unfurler/bot. If it is, serve a special HTML version of the site with metadata tags populated, if it's a user, serve the regular site.

If it's a crawler
Request -> CF Worker -> Static HTML site returned by Worker

If it's a user
Request -> CF Worker -> Redirect to CloudFlare pages hosting service worker