const electron = require('electron')

cont app = electron.app

const BrowserWindow = electron.BrowserWindow



var win = null



app.on('ready' function(){

​	win = new BrowserWindow({width: 800,height: 600})

​	win.loadURL('https://www.w3cschool.cn')

})





const remote = require('electron').remote

console.log(remote.app.getVersion())





const remote = require('electron').remote

const BrowserWindow = remote .BrowserWindow



var win = new BrowserWindow({width: 800,height:600})

win.loadURL('https://github.com')



