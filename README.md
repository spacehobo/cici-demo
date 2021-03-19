# readme-pages
Example for generating Github.io pages from Readme with Pandoc. We use the following:

- Pandoc's own [pandoc-action-example](https://github.com/pandoc/pandoc-action-example).
- [deploy-to-github-pages](https://github.com/marketplace/actions/deploy-to-github-pages) action.

The output can be viewed here: [github.io/readme-pages](https://jhidding.github.io/readme-pages)
Github repo: [github.com/jhidding/readme-pages](https://github.com/jhidding/readme-pages)

## Pandoc
Pandoc is the Universal document converter. We can use it to convert our README file into HTML.

        docker run pandoc/core pandoc --version

First try it locally:

        mkdir -p build
        docker run -v $PWD:/tmp pandoc/core -s /tmp/README.md \
                --metadata title="Hello, Pandoc" > build/index.html

Now we wish to run this command from a Github Action and upload the result to the `gh-pages` branch.

        name: Deploy pages
        on:
            push:
                branches:
                  - main
        jobs:
            deploy:
                runs-on: ubuntu-latest
                steps:
                    - name: checkout
                      uses: actions/checkout@v2
                    - name: prepare
                      run: |
                        mkdir -p build
                        touch build/.nojekyll   
                    - name: pandoc
                      uses: docker://pandoc/core:2.12
                      with:
                        args: >-
                          --standalone
                          --output=build/index.html
                          --metadata title="Hello, Pandoc"
                          README.md
                    - name: deploy
                      uses: JamesIves/github-pages-deploy-action@4.1.0
                      with:
                        branch: gh-pages
                        folder: build

