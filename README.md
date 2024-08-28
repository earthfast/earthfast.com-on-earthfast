## earthfast.com-on-EarthFast

The EarthFast landing page running on the EarthFast protocol with a serverless domain node. A domain node in EarthFast is a web server that responds to DNS requests with a service worker that loads the project's website content in a decentralized way and terminates SSL traffic.

This repo can also be used as a reference implementation to run your own website or frontend on EarthFast. While this is CloudFlare specific, it can be modified to run on most static asset hosting platforms including Github Pages, Vercel, CloudFront etc.

### How it works

When a user hits a EarthFast project's URL, the request is served by the project's domain node and initially serves the a site which has a loading spinner that loads a service worker in the background. The [service worker](https://github.com/earthfast/service-worker) subsequently fetches and renders the actual website content from the decentralized EarthFast protocol.


There's 3 pieces at play here (in order)
1. `.github/workflows/generate-service-worker.yml` - generates and commits the service worker code on a cron job if there are changes. Outputs the code to `earthfast-sw.js`
2. `build/earthfast/*` and `build/index.html` - initial HTML page that displays a loading spinner and calls for the service worker `earthfast-sw.js` in the background
3. `earthfast-sw.js` - calls the nodes in the decentralized network to fetch the appropriate content, do checks to make sure content wasn't tampered with etc and renders site to the user

### How can I use this?

1. Either clone or fork this repo to use as the base template. 
2. Set the following in your project settings
`PROJECT_ID` (Environment Variable) - EarthFast ProjectID
3. Connect to the static asset hosting service of your choice. While you can use any static asset hosting platform (Github Pages, Vercel, etc to host the assets from this repo), this is running via [Cloudflare Pages using Git](https://developers.cloudflare.com/pages/framework-guides/deploy-anything/#deploy-with-cloudflare-pages)
4. Attach a [Custom Domain](https://developers.cloudflare.com/pages/configuration/custom-domains/).


### Extensions

#### Custom Loading Screen

You can customize `build/index.html` and the assets from the `build/earthfast` folder.

#### Adding SEO 
SEO works by having a CloudFlare Worker proxy requests to the domain node and service worker. The Worker checks if the request is coming from a search engine crawler/link unfurler/bot. If it is, serve a special HTML version of the site with metadata tags populated, if it's a user, serve the regular EarthFast domain node and service worker.

If it's a crawler
Request -> CF Worker -> Static HTML site returned by Worker

If it's a user
Request -> CF Worker -> Redirect to CloudFlare pages hosting service worker

##### Setting up the SEO Proxy
1. Open [cloudflare-seo-worker.js](extensions/cloudflare-seo-worker.js) and edit the SITE_URL and STATIC_HTML variables
2. Deploy a CloudFlare Worker with the following code with the appropriate changes to the static HTML file.
3. Attach a [custom domain](https://developers.cloudflare.com/workers/configuration/routing/custom-domains/) to the Worker. You might have to move the domain node to a separate URL if you want to use that URL

#### Custom Headers
The `build/_headers` file specifies custom headers to send when the page is loaded. This is a CloudFlare specific file, more information is available [here](https://developers.cloudflare.com/pages/configuration/headers).