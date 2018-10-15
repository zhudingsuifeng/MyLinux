## build myphthon by ubuntu
```javascript
$docker run -v /home/fly/works:/home/fly/works -v /home/fly/git:/home/fly/git -it --name python ubuntu bash
#apt update
#apt install vim
#apt install python python3
#apt install python-pip python3-pip
#python2 -m pip install numpy pandas seaborn matplotlib
#apt install python3-mpltoolkits.basemap  #install basemap
#vi ~/.config/matplotlib/matplotlib
add:
backend:Agg
#python3 -m pip install pygal_map_world cairosvg
```
