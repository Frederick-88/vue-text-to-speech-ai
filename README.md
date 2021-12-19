## vue-text-to-speech-ai

### an AI about text to speech in Vue web application

### Netlify Deployment Issues :
#### Problem / Error Logs from Netlify :
```
...
gyp ERR! stack Error: `make` failed with exit code: 2
...
Error during NPM install
Build was terminated: Build script returned non-zero exit code: 1
...
```

#### Solution - Can try 1 by 1 :
1. Add Netlify Environment for `NODE_VERSION` then add your computed node version that you can find by entering `node -v` in your terminal
2. Delete package-lock.json
3. Clear cache & retry deploy