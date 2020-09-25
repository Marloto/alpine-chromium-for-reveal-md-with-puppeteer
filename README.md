# Container used to run Chromium and Puppeteer

An incomplete container for building Reveal-MD presentations, including PDF export.

## Usage

This example assumes, that you have something like this:

``` json
    "scripts": {
        "pdf": "reveal-md --css print.css --print-size 297x210mm"
    }
```

Thus, the individual docker container might look like this:

``` docker
FROM ...

# Create some working dir
WORKDIR /app

# Copy package informations
COPY package.json package-lock.json ./

# Install dependencies as required
RUN npm install

# Copy remaining application informations, add .dockerignore to exclude node_modules and other stuff
COPY . .

# Change ownership of working dir
RUN chown -R pptruser:pptruser /app

# Run everything after as non-privileged user.
USER pptruser

ENTRYPOINT ["npm", "run", "pdf", "--", "--puppeteer-chromium-executable", "/usr/bin/chromium-browser"]
```

## Run

``` bash
docker build -t your-reveal-md-build-container
docker run --rm -p 1948:1948 -v `pwd`/../$TARGET/_exportnew:/app/_export -it --cap-add=SYS_ADMIN your-reveal-md-build-container --print slides.pdf slides/slides.md
```