---
title: "Hello, World"
date: 2023-03-13T22:38:53-05:00
---

Hello, world. Hopefully, this blog will motivate me to ask more interesting
questions and to find and share satisfying answers. I also find that writing
forces me to clarify my thinking and move ideas from volatile to persistent
storage.

I think documenting how the site is built is a good place to start. I aim to
keep the stack as simple as possible, while reserving the freedom to swap out
components with minimal hassle.

- DNS registrar + name server: Cloudflare. Namecheap is also an option,
  though Cloudflare's additional free services and no-nonsense interface make it
  a pleasure to use.
- Hosting: GitHub pages. I chose this mostly out of familiarity. Cloudflare also
  has an impressive "Pages" product, but GitHub pages works fine for me.
- Front-end: Hugo static site generator with a slightly modified [etch](https://github.com/LukasJoswiak/etch)
  theme. I also tried out Jekyll, but ran into some issues with Ruby that
  reminded me how much I prefer static binaries.

The set-up was fairly straightforward.

1. Create a `joshklop.github.io` repository.
1. Register the domain name with Cloudflare (unnecessary if one is willing to
   have a `username.github.io` domain).

   I have what the [Cloudflare documentation](https://developers.cloudflare.com/dns/zone-setups/) 
   calls a "full setup", using Cloudflare for domain registration and
   authoritative DNS servers.
1. Add two CNAME records mapping `joshklop.com` and `www.joshklop.com` to
   `joshklop.github.io`. I set both to proxy through Cloudflare.

   Interestingly, the DNS RFC does not technically allow the "apex domain"
   `joshklop.com` to be used as the alias in a CNAME record. Cloudflare does
   some additional internal resolution ([CNAME flattening](https://developers.cloudflare.com/dns/zone-setups/))
   to allow it while still semantically matching the spec. GitHub Pages would
   otherwise [require](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain)
   us to follow the DNS spec by creating an A record mapping to a GitHub IP
   address, which could change. See [this](https://ouyi.github.io/post/2018/01/14/github-pages-cname-file.html)
   detailed post for the request-response flow with one A and one CNAME record.
1. Add a CNAME file to GitHub with the `www.joshklop.com` subdomain.

   GitHub will create a CNAME record mapping `joshklop.github.io` to
   `www.joshklop.com`. The post linked above shows the impact on requests and
   responses. Now, we should be able to access our site using `joshklop.com`,
   `www.joshklop.com`, or `joshklop.github.io`.
1. Configure security-related settings. Under the "SSL/TLS" tab in "Edge
   Certificates", enable "Always Use HTTPS", "Opportunistic Encryption", "TLS
   1.3", and "Automatic HTTPS Rewrites".
1. Hugo and theming. To install Hugo, I used:
   ```
   go install -tags extended github.com/gohugoio/hugo@latest
   ```
   The excellent [Hugo documentation](https://gohugo.io/documentation/) contains
   concise tutorials for different users' needs that aren't worth summarizing
   here.
1. Add a workflow to the repository to automatically build and deploy the site.
   I based mine on the [sample](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
   from the Hugo documentation.

All done!
