# KF version of MP-SfM

Github URL: `https://github.com/cvg/mpsfm`

Git clone recursive the repo first:
```
git clone --recursive https://github.com/cvg/mpsfm
```

Docker build
```
docker build -t kf-mpsfm:latest -f Dockerfile .
```

Docker push to hub
```
docker image tag kf-mpsfm:latest daidew/kf-mpsfm:latest
docker push daidew/kf-mpsfm:latest
```