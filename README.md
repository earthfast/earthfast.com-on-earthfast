## earthfast.com-on-EarthFast

The earthfast.com landing page entry point on the EarthFast protocol, an example of a domain node. A domain node in EarthFast is a server that responds to browser requests with a loading spinner (can be disabled), service worker and SSL termination. The service worker actually loads the project's website content on the browser in a decentralized way.

While this domain node is CloudFlare specific, it can be modified to run on most static asset hosting platforms including Github Pages, Vercel, CloudFront etc. Domain nodes are just a protocol and can also be run on Ubuntu VMs or K8s or anywhere else that can serve user requests.

### How it works

`User browser -> earthfast.com -> CloudFlare worker -> Return service worker to browser and run -> Fetch website from decentralized EarthFast content network -> Website loaded`

The service worker ([compiled](https://github.com/earthfast/earthfast.com-on-earthfast/blob/main/build/earthfast-sw.js) and [repo](https://github.com/earthfast/service-worker)) fetches and renders the actual website content from the decentralized EarthFast protocol.

The 3 relevant (in order they get used in this flow)
1. `.github/workflows/generate-service-worker.yml` - generates and commits the service worker code specific to your project on a cron. Outputs the code to `build/earthfast-sw.js`
2. `build/earthfast/*` and `build/index.html` - HTML page that displays a loading spinner and calls for the service worker `build/earthfast-sw.js` in the background
3. `earthfast-sw.js` - runs on user browser and calls the decentralized nodes to fetch content, do anti-tamper checks and renders site to the user

### How can I use this for my EarthFast project?

1. Either clone or fork this repo to use as the base template. 
2. Set the following in your project settings
`PROJECT_ID` (Environment Variable) - EarthFast ProjectID
3. Connect to the static asset hosting service of your choice. While you can use any static asset hosting platform (Github Pages, Vercel, etc to host the assets from this repo), this is running via [Cloudflare Pages using Git](https://developers.cloudflare.com/pages/framework-guides/deploy-anything/#deploy-with-cloudflare-pages)
4. Attach a [Custom Domain](https://developers.cloudflare.com/pages/configuration/custom-domains/).


### Optional Extensions

#### Custom Loading Screen

You can customize `build/index.html` and the assets from the `build/earthfast` folder. If you choose to customize the file, comment out the lines copying `earthfast-sw/dist/public` to `build` [here](https://github.com/earthfast/earthfast.com-on-earthfast/blob/main/.github/workflows/generate-service-worker.yml#L30-L33).

#### Adding SEO 
SEO works by having a CloudFlare Worker proxy requests to the domain node and service worker. The Worker checks if the request is coming from a search engine crawler/link unfurler/bot

If it's a crawler, serve a special HTML version of the site with metadata tags populated
Request -> CF Worker -> Static HTML site returned by Worker

If it's a user, return the regular service worker
Request -> CF Worker -> Redirect to CloudFlare Page hosting service worker

##### Setting up the SEO Proxy
1. Open [cloudflare-seo-worker.js](extensions/cloudflare-seo-worker.js) and edit the SITE_URL and STATIC_HTML variables
2. Deploy a CloudFlare Worker with the following code with the appropriate changes to the static HTML file.
3. Attach a [custom domain](https://developers.cloudflare.com/workers/configuration/routing/custom-domains/) to the Worker. You might have to move the domain node to a separate URL if you want to use that URL

#### Custom Headers
The `build/_headers` file specifies custom headers to send when the page is loaded. This is a CloudFlare specific file, more information is available [here](https://developers.cloudflare.com/pages/configuration/headers).
