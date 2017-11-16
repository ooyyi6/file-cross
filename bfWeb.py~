#!/usr/bin/env python

import os
import logging
import collections

from google.appengine.ext import db
from google.appengine.ext.webapp import template
import webapp2

logging.getLogger().setLevel(logging.DEBUG)

"""
====================
database definitions
====================
"""
class Task(db.Model):
    #tid = db.IntegerProperty()
    start = db.DateTimeProperty(auto_now_add=True)
    end = db.DateTimeProperty(auto_now=True)
    isdone = db.BooleanProperty(default=False)

class Log(db.Model):
    kid = db.ReferenceProperty(Task)
    pkg = db.StringProperty()
    atk = db.IntegerProperty()
    con = db.StringProperty()
    ver = db.FloatProperty()
    dev = db.StringProperty()
    time = db.DateTimeProperty(auto_now_add=True)

class Out:
    def __init__(self, pkg):
        self.pkg = pkg
        self.atk0A = ''
        self.atk0B = ''
        self.atk1 = ''
        self.atk2A = ''
        self.atk2B = ''
        self.atk3A = ''
        self.atk3B = ''
        self.atk4 = ''
        self.atk5 = ''
        self.atk6 = ''

"""
====================
Page functions
====================
"""
class MainPage(webapp2.RequestHandler):
    def get(self):
        self.response.headers['Content-Type'] = 'text/plain'
        self.response.write('Hello world!')

class TaskPage(webapp2.RequestHandler):
    def get(self):
        strkey = self.request.get('kid')
        if strkey:
            isall = False
            key = db.Key(strkey)
            tasks = db.GqlQuery("SELECT * FROM Task WHERE __key__ = :1", key)
        else:
            isall = True
            tasks = db.GqlQuery("SELECT * FROM Task ORDER BY start DESC")

        template_values = {
                'tasks': tasks,
                'isall': isall,
        }
        path = os.path.join(os.path.dirname(__file__), 'templates/task.html')
        self.response.out.write(template.render(path, template_values))

class LogPage(webapp2.RequestHandler):
    def get(self):
        strkey = self.request.get('kid')
        strkey2 = self.request.get('kid2')

        if strkey and strkey2:
            key = db.Key(strkey)
            key2 = db.Key(strkey2)
            keylist = [key, key2]
            logs = db.GqlQuery("SELECT pkg, atk, con, ver FROM Log WHERE kid IN :1 ORDER BY time ASC", keylist)
        elif strkey:
            key = db.Key(strkey)
            logs = db.GqlQuery("SELECT pkg, atk, con, ver FROM Log WHERE kid = :1 ORDER BY time ASC", key)

        if logs:
            dicts = collections.OrderedDict()
            for log in logs:
                if log.atk == 7:
                    continue

                if log.pkg not in dicts:
                    dicts[log.pkg] = Out(log.pkg)
                out = dicts[log.pkg]

                if log.con:
                    value = 'Y'
                else:
                    value = 'n'

                # atk 0
                if log.atk == 0:
                    if log.ver < 4.1:
                        if out.atk0A == 'n' and value == 'Y':
                            out.atk0A = 'Y'
                        elif out.atk0A == '':
                            out.atk0A = value
                    else:
                        if out.atk0B == 'n' and value == 'Y':
                            out.atk0B = 'Y'
                        elif out.atk0B == '':
                            out.atk0B = value
                # atk 1
                elif log.atk == 1:
                    if out.atk1 == 'n' and value == 'Y':
                        out.atk1 = 'Y'
                    elif out.atk1 == '':
                        out.atk1 = value
                # atk 2
                elif log.atk == 2:
                    if log.ver < 4.1:
                        if out.atk2A == 'n' and value == 'Y':
                            out.atk2A = 'Y'
                        elif out.atk2A == '':
                            out.atk2A = value
                    else:
                        if out.atk2B == 'n' and value == 'Y':
                            out.atk2B = 'Y'
                        elif out.atk2B == '':
                            out.atk2B = value
                # atk 3
                elif log.atk == 3:
                    if log.ver < 4.1:
                        if out.atk3A == 'n' and value == 'Y':
                            out.atk3A = 'Y'
                        elif out.atk3A == '':
                            out.atk3A = value
                    else:
                        if out.atk3B == 'n' and value == 'Y':
                            out.atk3B = 'Y'
                        elif out.atk3B == '':
                            out.atk3B = value
                # atk 4
                elif log.atk == 4:
                    if out.atk4 == 'n' and value == 'Y':
                        out.atk4 = 'Y'
                    elif out.atk4 == '':
                        out.atk4 = value
                # atk 5
                elif log.atk == 5:
                    if out.atk5 == 'n' and value == 'Y':
                        out.atk5 = 'Y'
                    elif out.atk5 == '':
                        out.atk5 = value
                # atk 6
                elif log.atk == 6:
                    if out.atk6 == 'n' and value == 'Y':
                        out.atk6 = 'Y'
                    elif out.atk6 == '':
                        out.atk6 = value

            outs = dicts.values()
            template_values = {
                'taskkey': strkey,
                'taskid': key.id(),
                'outs': outs
            }
            path = os.path.join(os.path.dirname(__file__), 'templates/log.html')
            self.response.out.write(template.render(path, template_values))

"""
====================
Handler functions
====================
"""
class StartHandler(webapp2.RequestHandler):
    def get(self):
        logging.warning('StartHandler')
        sec = self.request.get('sec')
        if sec == 'abc123cde456':
            task = Task()
            key = task.put()

            self.response.set_status(200)
            strkey = str(key)
            self.response.out.write(strkey)

class EndHandler(webapp2.RequestHandler):
    def get(self):
        logging.warning('EndHandler')
        strkey = self.request.get('kid')
        if strkey:
            key = db.Key(strkey)

            task = db.get(key)
            task.isdone = True
            task.put()
        
            self.response.set_status(200)

class SendHandler(webapp2.RequestHandler):
    def get(self):
        logging.warning('SendHandler')
        kid = self.request.get('kid')
        pkg = self.request.get('pkg')
        atk = self.request.get('atk') #shoule be '0' -- '3'
        con = self.request.get('con') #maybe empty
        ver = self.request.get('ver')

        # for handling invalid literal for float(): 4.0.2
        ver = ver[0:3]

        if kid and pkg and atk and ver:
            key = db.Key(kid)
            log = Log()
            log.kid = key
            log.pkg = pkg
            log.atk = int(atk)
            log.con = con
            log.ver = float(ver)
            log.put()

            self.response.set_status(200)

class ReqHandler(webapp2.RequestHandler):
    def get(self):
        logging.warning('ReqHandler')
        kid = self.request.get('kid')
        pkg = self.request.get('pkg')
        atk = self.request.get('atk') #should be '4', '5', '6'
        con = self.request.get('con') #'reqflag' or empty
        ver = self.request.get('ver')

        # for handling invalid literal for float(): 4.0.2
        ver = ver[0:3]

        if kid and pkg and atk and ver:
            key = db.Key(kid)
            log = Log()
            log.kid = key
            log.pkg = pkg
            log.atk = int(atk)
            log.con = con
            log.ver = float(ver)
            log.put()
            
            self.redirect('/static/images/hello.png')

"""
====================
Main entry
====================
"""
app = webapp2.WSGIApplication([
        ('/', MainPage),
        ('/task', TaskPage),
        ('/log', LogPage),
        ('/start', StartHandler),
        ('/end', EndHandler),
        ('/send', SendHandler),
        ('/req', ReqHandler),
        ],
        debug=True)
