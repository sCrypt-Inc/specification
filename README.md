
# How to pull/push to transifex
- install nodejs
- install python pip
- `sudo pip3 install -U transifex-client`
- `sudo pip3 install -U sphinx`
- `sudo pip3 install -U pygments-lexer-solidity`
- `sudo pip3 install -U sphinx_rtd_theme`
- go to get a new [api token](https://www.transifex.com/user/settings/api)
- cd docs
- `npm run pull:all`
- input api token: 