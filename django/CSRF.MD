## CSRF

网站A:  金钱交易
www.jinqian.com

网站B:  非法网站
www.jinqian.com

## flask 中的CSRF
flask_wtf : 自带CSRF保护功能,强制 程序员设置秘钥


## Django中的CSRF

CsrfViewMiddleware 实现自动保护

1. 生成token值
2. 验证token值