# TodoApp
from kivy.lang import Builder
from kivymd.app import MDApp
from kivy.uix.screenmanager import ScreenManager
import datetime
from datetime import date
from kivy.properties import StringProperty
from kivy.core.window import Window
from kivymd.uix.behaviors import CommonElevationBehavior
from kivymd.uix.floatlayout import MDFloatLayout
from kivymd.uix.snackbar import Snackbar
from kivy.metrics import dp,sp
KV='''
<TodoCard>
    elevation:10
    md_bg_color:1,1,1,1
    radius:[8]
    size_hint_y:.3
    MDFloatLayout:
        id: bar
        size_hint:.01,.9
        pos_hint: {'center_x':.02,'center_y':.5}
        md_bg_color:1,170/255,23/255
        
    MDLabel:
        text:root.title
        markup:True
        font_size:"20sp"
        size_hint_x:.8
        pos_hint: {'center_x':.46,'center_y':.8}
        
    MDCheckbox:
        size_hint: None,None
        size:"48dp","48dp"
        unselected_color:1,170/255,23/255,1
        selected_color:1,179/255,0,1
        pos_hint: {'center_x':.95,'center_y':.8}
        on_active:app.on_complete(*args,description,bar)
        
    MDLabel:
        id:description     
        text:root.description
        markup:True
        font_size:"20sp"
        size_hint_x:.9
        line_height:.8
        pos_hint: {'center_x':.51,'center_y':.4}
        
MDScreen:
    date:date
    todo_list:todo_list
    name: 'MainScreen'
    MDFloatLayout:
        md_bg_color:1,1,1,1
        MDLabel:
            text:"MY Tasks"
            pos_hint: {'center_x':.56,'center_y':.95}
            font_size:"35sp"
            
        MDLabel:
            id:date
            text:""
            pos_hint: {'center_x':.565,'center_y':.89}
            font_size:"18sp"
            
        MDIconButton:
            icon:"plus"
            pos_hint: {'center_x':.89,'center_y':.925}
            user_font_size:"30sp"
            md_bg_color:1,170/255,23/255,1
            theme_text_color:"Custom"
            text_color:1,1,1,1
            on_release:
                root.manager.transition.direction = "left"
                root.manager.current = "add_todo"
            
        ScrollView:
            do_scroll_y:True
            do_scroll_x:False
            size_hint_y:.85
            pos_hint: {'center_x':.5,'y':0}
            bar_width:0
            MDGridLayout:
                id:todo_list
                cols:1
                height:self.minimum_height
                row_default_height:80
                size_hint_y:None
                padding:15,10
                spacing:15,10
                    
'''

KV2='''
MDScreen:
    description:description
    name: "add_todo"
    MDFloatLayout:
        md_bg_color:1,1,1,1
        MDIconButton:
            icon:"chevron-left"
            user_font_size:"40sp"
            pos_hint: {'center_y':.95}
            theme_text_color:"Custom"
            text_color:1,170/255,23/255,1
            on_release:
                root.manager.transition.directionlen(description) <61 = "right"
                root.manager.current = "MainScreen"
                
        MDLabel:
            text:"Add Task"
            pos_hint: {'center_x':.58,'center_y':.88}
            font_style:"H3"
            
        MDFloatLayout:
            size_hint:.85,.08
            pos_hint: {'center_x':.5,'center_y':.78}
            canvas:
                Color:
                    rgb:238/255,238/255,238/255
                RoundedRectangle:
                    pos:self.pos
                    size:self.size
                    radius:[25]
            MDTextField:
                id:title
                hint_text: "Title"
                size_hint:1,None
                mode:"round"
                pos_hint: {'center_x':.5,'center_y':.5}
                height:self.minimum_height
                multiline:False
                cursor_color:1,170/255,23/255,1
                cursor_width:"2sp"
                foreground_color:1,170/255,23/255,1
                background_color:0,0,0,0
                padding:15
                
        MDFloatLayout:
            size_hint:.85,.28
            pos_hint: {'center_x':.5,'center_y':.57}
            canvas:
                Color:
                    rgb:238/255,238/255,238/255
                RoundedRectangle:
                    pos:self.pos
                    size:self.size
                    radius:[25]
            MDTextField:
                id:description
                hint_text: "Description"
                size_hint:1,None
                pos_hint: {'center_x':.5,'center_y':.5}
                height:self.minimum_height
                multiline:False
                cursor_color:1,170/255,23/255,1
                cursor_width:"2sp"
                foreground_color:1,170/255,23/255,1
                background_color:0,0,0,0
                padding:15
        MDFlatButton:
            
            text:"Add Task"
            size_hint:.45,.08
            pos_hint: {'center_x':.5,'center_y':.36}
            background_color:0,0,0,0
            font_size:"18sp"
            on_release:
                app.add_todo(title.text, description.text)
            
            canvas.before:
                Color:
                    rgba:1,170/255,23/255,1
                RoundedRectangle:
                    pos:self.pos
                    size:self.size
                    radius:[25]
                        
                    
                    
                        
            
'''
class TodoCard(CommonElevationBehavior,MDFloatLayout):
    title = StringProperty()
    description = StringProperty()
class TestApp(MDApp):
    Window.size = (800,600)
    def build(self):
        global screen_manager
        screen_manager = ScreenManager()
        screen_manager.add_widget(Builder.load_string(KV))
        screen_manager.add_widget(Builder.load_string(KV2))
        return screen_manager

    def on_start(self):
        today = date.today()
        wd = date.weekday(today)
        days = ["Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"]
        year = str(datetime.datetime.now().year)
        month = str(datetime.datetime.now().strftime("%b"))
        day = str(datetime.datetime.now().strftime("%d"))
        screen_manager.get_screen("MainScreen").date.text = f"{days[wd]}, {day} {month} {year}"

    def add_todo(self,title,description):
        if title != "" and description != "" and len(title) < 21 and len(description) <61:
            screen_manager.current="MainScreen"
            screen_manager.transition.direction = "right"
            screen_manager.get_screen("MainScreen").todo_list.add_widget(TodoCard(title=title,description=description))
        elif title =="":
            Snackbar(text="Title is missing",snackbar_x="10dp",snackbar_y="10dp",size_hint_y=.08,size_hint_x=(Window.width - (dp(10)*2))/Window.width,bg_color=(1,170/255,23/255,1), font_size="18sp").open()
        elif len(title) > 21:
            Snackbar(text="Title length should be < 21",snackbar_x="10dp",snackbar_y="10dp",size_hint_y=.08,size_hint_x=(Window.width - (dp(10)*2))/Window.width,bg_color=(1,170/255,23/255,1), font_size="18sp").open()
        elif len(description) > 61:
            Snackbar(text="Description length should be < 61",snackbar_x="10dp",snackbar_y="10dp",size_hint_y=.08,size_hint_x=(Window.width - (dp(10)*2))/Window.width,bg_color=(1,170/255,23/255,1), font_size="18sp").open()

    def on_complete(self,checkbox,value,description,bar):
        if value:
            description.text=f"[s]{description.text}[\s]"
            bar.md_bg_color=0,170/255,0,1

        else:
            remove=["[s]", "[\s]"]
            for i in remove:
                description.text=description.text.replace(i, "")
                bar.md_bg_color=1,170/255,23/255,1


TestApp().run()

