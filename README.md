install node and npm (node version <= 12)

npm install

npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server

hexo new page-title
hexo g
hexo s
hexo deploy


copy local ssh pub.isa to github.com

# 如果没有权限
ssh -T git@github.com