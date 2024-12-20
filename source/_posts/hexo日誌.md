---
title: hexo日誌
date: 2024-12-12 13:39:58
tags:
---
## 問題1
在本地運行
'''
Hexo server 
'''
可行

但上傳至github再=生成page時遭遇build error

'''
Logging at level: debug Configuration file: /github/workspace/./_config.yml Theme: landscape github-pages 232 | Error: The landscape theme could not be found. 
'''

原因是theme floder下沒有landscape主題的資料

本地運行時，會使用Hexo 的默認設置和緩存，在

'''
node_modules/hexo-theme-landscape/
'''
目錄下


'''bash
class RegistrationForm(FlaskForm):
    username = StringField('Username', validators=[DataRequired()])
    
    # 這個方法名稱是 validate_ + username(欄位名)
    def validate_username(self, username):
        user = db.session.scalar(sa.select(User).where(
            User.username == username.data))
        if user is not None:
            raise ValidationError('Please use a different username.')
'''
當你添加任何符合 validate_<field_name> 模式的方法時，WTForms 會將其視為自定義驗證器，並在內建驗證器之外調用它們。
所以在這個例子裡，實際上username欄位有兩個驗證器
- 內建驗證器：DataRequired(): 確保欄位不為空
- 自定義驗證器：檢查用戶名是否已存在於資料庫
