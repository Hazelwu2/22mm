# README

## Branch
- master：發布文章
- hexo：需要git pull下來時，在不同電腦上使用此Hexo

## 在不同電腦上發布
git clone https://github.com/Hazelwu2/22mm.git
cd 22mm
npm install hexo
npm install
npm install hexo-deployer-git -save
寫好文章、部署後
git add .
git commit -m 'Message'
git push origin hexo