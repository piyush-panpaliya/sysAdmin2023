## Challenge 5 - Docker Deployments

ok this challenge seemed very easy at first but there were a lot of issue for ruby app.

### SAIC website

this was a simple one since it was all static files we can just use nginx to serve the file

```
FROM nginx:latest

RUN rm -rf /usr/share/nginx/html/*
COPY . /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- get the nginx image
- empty all content of `/usr/share/nginx/html/` folder
- copy the content of website to `/usr/share/nginx/html/`
- open port 80 of container to host
- run nginx in foreground to see logs

well this was like butter but now for Ruby app

### Ruby (github languages)

first of all cloned it and made a simple docker file obviously with `ruby:latest` image but to my surprise the repo is 9 years old so had to change the version of ruby `2.3.1`. Well everything worked but then bundler version didnt matched so changed it manually with `gem install bundler -v "1.12.5" `.

But now a big error popped up. `execjs` dep required a js runtime installed so tried installing nodejs but as apt has an old version of nodejs tried using `nodesource` but this too didnt worked then tried apt one itself but apparently the ubuntu image was so old that the apt repo url were all down now and didnt worked. so searched if there are any unofficial image that has ruby and nodejs installed but found these script to install nodejs

```
RUN \
  cd /tmp && \
  wget http://nodejs.org/dist/node-latest.tar.gz && \
  tar xvzf node-latest.tar.gz && \
  rm -f node-latest.tar.gz && \
  cd node-v* && \
  ./configure && \
  CXX="g++ -Wno-unused-local-typedefs" make && \
  CXX="g++ -Wno-unused-local-typedefs" make install && \
  cd /tmp && \
  rm -rf /tmp/node-v* && \
  npm install -g npm && \
  printf '\n# Node.js\nexport PATH="node_modules/.bin:$PATH"' >> /root/.bashrc
```

but this installed python3 and since the image was OLD it had py2 and I counldn't even update it since apt wasnt working.

But then found this https://github.com/rails/execjs repo and saw bun is supported and since installing it is just running a bash script so tried this but again it required unzip which isnt preinstalled and to install it apt was required so tried to find an old deb file for unzip and found it on `http://security.ubuntu.com/ubuntu/pool/main/u/unzip/` and now just curl it and install with dpkg and

**YES** now I have bun installed BUT totally forgot that the gemfile had old dependencies so naturally `execjs` that required the js runtime was old and bun wasnt there around that time so it wasnt detected and now I was again confused and just left this challenge and scratched my head over chal 1

now a fresh start the next day and I had this crazy idea

### EUREKA GOD MOVE

since the repo was old like 9 years old I thought why not time travel to see what could be done, yeah am not kidding all thanks to git `https://github.com/rails/execjs/tree/9e7abd095e2c0d3cd4f497384c9adf9d84f69ac3?tab=readme-ov-file` is where I landed, a 9 year old commit and now I found `therubyracer` a runtime which is now deprecated but not for my repo.

just added `therubyracer` to my gemfile and BOOM everything now works
and I had my website up and running but now it required postgres server running which I ignored since that wasnt necesaary for the challenge

### Network type

I am using bridge network since I want to keep my containers isloadted from other containers and only exposed to host via the the ports required by the app
