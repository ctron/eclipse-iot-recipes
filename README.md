# Eclipse IoT recipes

Recipes for deploying Eclipse IoT as an integrated solution.

Also see: [Eclipse IoT integration map](https://ctron.github.io/eclipse-iot-integration-map/).

The content of this repository is published via GitHub pages, and can be viewed here: https://ctron.github.io/eclipse-iot-recipes/

## Creating the content

Single run, building the content:

    docker run --rm --volume="$PWD:/srv/jekyll:z" --volume="$PWD/vendor/bundle:/usr/local/bundle:z" -it jekyll/jekyll:3.8 jekyll build

Serving the content locally. Run the following command can connect to [http://localhost:4000](http://localhost:4000):

    docker run --rm --volume="$PWD:/srv/jekyll:z" --volume="$PWD/vendor/bundle:/usr/local/bundle:z" -p 4000:4000 -it jekyll/jekyll:3.8 jekyll serve

