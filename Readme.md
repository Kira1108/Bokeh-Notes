# Bokeh

**Tell bokeh not to open a new tab in Jupyter notebook**
```python
import bokeh.io
bokeh.io.reset_output()
bokeh.io.output_notebook()
```

### Typical import
**Always import these things**
```python
from bokeh.io import output_file, show, output_notebook
from bokeh.plotting import figure
```


### Simple plot
```python
# this is a plot
plot = figure(plot_width = 800,
              plot_height = 600, 
              tools = 'pan,box_zoom' )

# data on the plot
plot.circle([1,2,3,4,5],[8,5,6,2,3])

# save and display plot
output_file('circle.html')
show(plot)


# this is another plot
plot = figure(plot_width = 400,
              plot_height = 300, 
              tools = 'pan,box_zoom' )

# x is a vectorized value, y and size works on each point
plot.circle(x = 10,y = [8,5,6,2,3], size = [1,10,20,50,100],fill_color='red')

# save and display plot
output_notebook()
show(plot)
```
![](Readme/81AC82BA-945F-40B6-8EB7-9992E0256CEF.png)



### Axis names
```python
df = pd.read_csv('data/literacy_birth_rate.csv')[['fertility','female literacy']].dropna()
fertility = df['fertility'].values
female_literacy = df['female literacy'].values


plot = figure(x_axis_label='fertility (children per woman)',
              y_axis_label='female_literacy (% population)')

plot.circle(fertility, female_literacy)
output_notebook()
show(plot)
```

![](Readme/4FDED3E5-B08A-4901-BC4F-476D7B9B2F57.png)



### Multiple plot on single figure

```python
latin_df = df[df.Continent == 'LAT'][['fertility','female literacy']].dropna()
africa = df[df.Continent == 'AF'][['fertility','female literacy']].dropna()


fertility_latinamerica = latin_df['fertility'].values
female_literacy_latinamerica = latin_df['female literacy'].values
fertility_africa = africa['fertility'].values
female_literacy_africa = africa['female literacy'].values


p = figure(x_axis_label='fertility (children per woman)',
              y_axis_label='female_literacy (% population)')

p.circle(fertility_latinamerica,female_literacy_latinamerica, size = 20)

# Add an x glyph to the figure p
p.x(fertility_africa,female_literacy_africa, size = 20)

output_file('fertility.html')
show(p)
```

![](Readme/93D945D2-D393-4091-BA6F-5AB55533AD54.png)


### Customization: Color, Alpha and Size

Bokeh accepts colors as hexadecimal strings, tuples of RGB values between 0 and 255, and any of the 147  [CSS color names](http://www.colors.commutercreative.com/grid/) . Size values are supplied in screen space units with 100 meaning the size of the entire figure.

The alpha parameter controls transparency. It takes in floating point numbers between 0.0, meaning completely transparent, and 1.0, meaning completely opaque.

```pyhton
# Create the figure: p
p = figure(x_axis_label='fertility (children per woman)', y_axis_label='female_literacy (% population)')

# Add a blue circle glyph to the figure p
p.circle(fertility_latinamerica, female_literacy_latinamerica, color = 'blue', size = 10, alpha = 0.8)

# Add a red circle glyph to the figure p

p.circle(fertility_africa,female_literacy_africa,color = 'red',size = 10,alpha = 0.8)
# Specify the name of the file
output_file('fert_lit_separate_colors.html')

# Display the plot
show(p)
```
![](Readme/45C94D4A-D815-4BC6-99FF-1EA5D6B0CE5F.png)


### Glyphs
line plot with markers
`line_width`， `fill_color`, `size`
```python
# example 1
p = figure()
x = [1,2,3,4,5]
y = [6,7,8,9,10]
p.line(x,y,line_width = 4)
p.circle(x,y, fill_color ='white', size = 10)
output_notebook()
show(p)

# exmaple 2
df = pd.read_csv('data/aapl.csv', parse_dates = ['date'])
dates = df.date.values[-100:]
prices = df.close.values[-100:]
p = figure(x_axis_type = 'datetime', x_axis_label='Date', y_axis_label='US Dollars')
p.circle(dates, prices, fill_color='white', size=4)
p.line(dates, prices)
show(p)
```

![](Readme/2BE42EDD-BE57-46D6-9681-33A5395E29C2.png)![](Readme/78BE47FE-DACB-4821-9E90-84B4A345CA63.png)



### Column data source
Map string column names to sequence of data

```python
# create by specifying iterabls
from bokeh.models import ColumnDataSource
source = ColumnDataSource(data = {'x':[1,2,3,4,5],'y':[6,7,8,9,10]})

# create using pandas dataframe
from bokeh.sampledata.iris import flowers
iris = flowers.copy()
source = ColumnDataSource(flowers)
```


Using column data source
```python
# Import the ColumnDataSource class from bokeh.plotting
from bokeh.plotting import ColumnDataSource

df = pd.read_csv('data/sprint.csv').dropna()


# Create a ColumnDataSource from df: source
source = ColumnDataSource(df)

p = figure()
# Add circle glyphs to the figure p
p.circle('Year','Time',color = 'color',source = source,size = 8)

# Specify the name of the output file and show the result
output_file('sprint.html')
show(p)
```

![](Readme/78BD729B-7F38-460B-9B0D-EFE661FE4157.png)

### Visual appearance

**Enable selection of data on visualizations**
```python
from bokeh.sampledata.iris import flowers
from bokeh.models import ColumnDataSource

source = ColumnDataSource(flowers)
p = figure(tools = 'box_select, lasso_select')
p.circle('petal_length', 'petal_width', 
         source= source, 
         selection_color = 'red', 
         nonselection_fill_alpha = 0.2,
         nonselection_fill_color = 'grey')
show(p)


# or you can add tools to existing tools list
from bokeh.sampledata.iris import flowers
from bokeh.models import ColumnDataSource
from bokeh.models import BoxSelectTool, LassoSelectTool

source = ColumnDataSource(flowers)
p = figure()
p.circle('petal_length', 'petal_width', 
         source= source, 
         selection_color = 'red', 
         nonselection_fill_alpha = 0.2,
         nonselection_fill_color = 'grey')

p.add_tools(BoxSelectTool())
p.add_tools(LassoSelectTool())
show(p)
```
![](Readme/8C6FCF08-B83E-4C64-BD8E-24EA2CF2D366.png)


**Hover**
```python
from bokeh.models import HoverTool
hover = HoverTool(tooltips = None, mode ='hline')

data = np.random.random(size = (1000,2))
x = data[:,0]
y = data[:,1]

p = figure(title = 'Random plot', tools = [hover,'crosshair'])
p.circle(x,y,size = 15, alpha = 0.5, hover_color = 'red')
show(p)
```
![](Readme/34142947-9B8F-4A6D-A6C5-72D98F1344C3.png)

**Hover time series**
Use `p.add_tools` to add other tools into default tools list

```python
source = ColumnDataSource(pd.read_csv('data/aapl.csv',parse_dates = ['date']))

hover = HoverTool(mode = 'vline')

p = figure(x_axis_type = 'datetime')
p.circle('date','close', source = source, fill_color = 'grey', line_color = None, alpha = 0.1,
         hover_alpha = 0.5, hover_line_color = 'white' , hover_fill_color = 'firebrick', size = 8)
p.add_tools(hover)
show(p)
```
![](Readme/C5A07CB9-78B7-42C7-932E-C01A19A288D9.png)



**Color Mapper**
```python
from bokeh.sampledata.iris import flowers
from bokeh.models import CategoricalColorMapper
source = ColumnDataSource(flowers)
mapper = CategoricalColorMapper(
    factors = ['setosa', 'versicolor', 'virginica'], 
    palette = ['red', 'green','blue']
)

p = figure(x_axis_label = 'petal_length', y_axis_label = 'sepal_length')
p.circle('petal_length', 'sepal_length', size = 10, source = source,
        color = {'field':'species', 'transform':mapper})
show(p)
```
![](Readme/B0F687CF-A27E-4EBA-9F00-96FA7B9E736D.png)

**Use a legend**
```python
#Import CategoricalColorMapper from bokeh.models
from bokeh.models import CategoricalColorMapper

# Convert df to a ColumnDataSource: source
source = ColumnDataSource(df)

# Make a CategoricalColorMapper object: color_mapper
color_mapper = CategoricalColorMapper(factors =['Europe', 'Asia', 'US'],
                                      palette =['red', 'green', 'blue'])

# Add a circle glyph to the figure p
p.circle('weight', 'mpg', source=source,
            color= dict(field= 'origin', transform = color_mapper),
            legend ='origin')

# Specify the name of the output file and show the result
output_file('colormap.html')
show(p)
```


### Layout / Multiple plots

```python
from bokeh.layouts import row, column
```

And then
```python

from bokeh.sampledata.iris import flowers
from bokeh.palettes import Category20_20
from bokeh.layouts import row


def make_categorical_mapper(data, col):
    factors = data[col].unique()
    colors = Category20_20[:len(factors)]
    return CategoricalColorMapper(factors = factors, 
                                  palette = colors)  


source = ColumnDataSource(flowers)
mapper = make_categorical_mapper(flowers,'species')

p1 = figure(title = 'petal_length vs sepal_width', plot_width = 500)
p1.circle('petal_length','sepal_length', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, )

p2 = figure(title = 'sepal_length vs petal_width', plot_width = 500)
p2.circle('sepal_length','petal_width', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, )


p3 = figure(title = 'sepal_length vs sepal_width', plot_width = 500)
p3.circle('sepal_length','sepal_width', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, )


```

add layout and show
```python
layout = row(p1,p2,p3)
show(layout)
```

![](Readme/9203B6D3-E5A8-4E98-AE60-E43DE5858E0B.png)


### Advanced layout

**Grid Plot**
```python
from bokeh.layouts import gridplot
```

```python

from bokeh.sampledata.iris import flowers
from bokeh.palettes import Category20_20
from bokeh.layouts import row


def make_categorical_mapper(data, col):
    factors = data[col].unique()
    colors = Category20_20[:len(factors)]
    return CategoricalColorMapper(factors = factors, 
                                  palette = colors)  


source = ColumnDataSource(flowers)
mapper = make_categorical_mapper(flowers,'species')

p1 = figure(title = 'petal_length vs sepal_width', plot_width = 500, plot_height = 500)
p1.circle('petal_length','sepal_length', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, )

p2 = figure(title = 'sepal_length vs petal_width', plot_width = 500, plot_height = 500)
p2.circle('sepal_length','petal_width', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, )


p3 = figure(title = 'sepal_length vs sepal_width', plot_width = 500, plot_height = 500)
p3.circle('sepal_length','sepal_width', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, )

p4 = figure(title = 'petal_length vs petal_width',plot_width = 500, plot_height = 500)
p4.circle('petal_length','petal_width', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, )
```

Put figure into grid plot
```python
layout = gridplot([[p1, p2],[p3, p4]])
show(layout)

```

**Tabbed layout**
```python
from bokeh.models.widgets import Panel,Tabs
from bokeh.layouts import row, column

first = Panel(child = row(p1, p2), title = 'first' )
second = Panel(child = row(p3, p4), title = 'second')

tabs = Tabs(tabs = [first, second])
show(tabs)
```



**Legend**
```python
source = ColumnDataSource(flowers)
mapper = make_categorical_mapper(flowers,'species')

p1 = figure(title = 'petal_length vs sepal_width', plot_width = 500, plot_height = 500)
p1.circle('petal_length','sepal_length', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, legend_field = 'species')

p1.legend.location = 'top_left'

p.legend.background_fill_color = 'lightgray'

show(p1)
```

![](Readme/A688C8E9-03FE-4744-B911-0412F4D970D5.png)

**Hover tooltips**
```python
from bokeh.models import HoverTool


hover = HoverTool(
tooltips = [
    ('species name','@species'),
    ('petal length', '@petal_length'),
    ('sepal length','@sepal_length')
])

source = ColumnDataSource(flowers)
mapper = make_categorical_mapper(flowers,'species')

p1 = figure(title = 'petal_length vs sepal_width', plot_width = 500, plot_height = 500, tools = [hover, 'pan','wheel_zoom'])
p1.circle('petal_length','sepal_length', 
          color = dict(field = 'species',  transform = mapper),line_color = None, 
          source = source, size = 10, legend_field = 'species')

p1.legend.location = 'top_left'
show(p1)
```

![](Readme/F4374B10-6502-42DF-B740-2E6EAFF210B9.png)



### Curdoc
```python
# Perform necessary imports
from bokeh.io import curdoc
from bokeh.plotting import figure

# Create a new plot: plot
plot = figure()

# Add a line to the plot
plot.line([1,2,3,4,5],[2,5,4,6,7])

# Add the plot to the current document
curdoc().add_root(plot)

```

```bash
bokeh serve --show myapp.py
bokeh serve --show myappdir/
```




