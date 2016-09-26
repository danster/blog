# How to draw a topology graph by Dot
<!-- toc -->
### Dot language

```
	graph	:	[ strict ] (graph | digraph) [ ID ] '{' stmt_list '}'
 stmt_list	:	[ stmt [ ';' ] [ stmt_list ] ]
	stmt	:	node_stmt
			|	edge_stmt
			|	attr_stmt
			|	ID '=' ID
			|	subgraph
attr_stmt	:	(graph | node | edge) attr_list
attr_list	:	'[' [ a_list ] ']' [ attr_list ]
	a_list	:	ID '=' ID [ (';' | ',') ] [ a_list ]
edge_stmt	:	(node_id | subgraph) edgeRHS [ attr_list ]
	edgeRHS	:	edgeop (node_id | subgraph) [ edgeRHS ]
node_stmt	:	node_id [ attr_list ]
	node_id	:	ID [ port ]
	port	:	':' ID [ ':' compass_pt ]
    		|	':' compass_pt
subgraph	:	[ subgraph [ ID ] ] '{' stmt_list '}'
compass_pt	:	(n | ne | e | se | s | sw | w | nw | c | _)
```

无向图:  

```
graph G {
	 a -- b -- c; # 无向边
	 b -- d; # 无向边
}
```  
有向图:  

```
digraph G {
	 a -> b -> c; # 有向边
	 b -> d; # 有向边
}
```

更多示例和语法请参见:  
- [Wikipedia: DOT](http://en.wikipedia.org/wiki/DOT_\(graph_description_language\))  
- [Graphviz: The DOT Language](http://www.graphviz.org/doc/info/lang.html)

## Node, Edge, Graph

Dot 语言有点(N:node),边(E:edge),图组成,边只能链接两点, 且分无向边和有向边,
图也分 根图(G:root graph), 子图(S:subgraphs), 聚类子图(C: cluster subgraphs)

N,E,G,S,C 都有自己的属性,有些属性有默认值,有些需要设置,参见[Node, Edge and Graph Attributes](http://www.graphviz.org/doc/info/attrs.html)  

请看下例(test.dot):  

```

// root graph gname
digraph gname {
    //root graph attr
    label="transport tonology" 
    overlap=prism
    clusterrank=local; //mode used for handling clusters
    pagedir=LT; // "BL", "BR", "TL", "TR", "RB", "RT", "LB", "LT"
    rankdir=TB //"TB", "LR", "BT", "RL", corresponding to directed graphs drawn 
    node [shape=plaintext]; //node attr
    edge [fontcolor=red, labelfloat=true]; //union egde attts, the default separator char is space or ','
    A1 -> A2 [dir=none label="passway"]; //special edge attrs
    B1 -> B2 [label="path"];
    B11 -> B12 [label="highway" dir=both];
    { rank=10; A1 A2 } //Rank constraints on the nodes in a subgraph
    { rank=11; B1 B2 }
    { rank=12; B11 B12 }
    
    // subgraph
    subgraph sname {
    	node [shape=box]
    	{rank=min; S1, S2, S3, S4, S5}
        {rank=21; Nanjing, Wuxi}
        
        Nanjing -> S1 -> S2 [dir=none]
        Nanjing -> S3
        Nanjing -> S4
        
    }

    // cluster subgraph
    subgraph cluster_cname {
        // cluster subgraph attr
        rank=sink
        color="#C0C0C0";
        style=filled; //Set style information for components of the graph
        label="big cities' network";
        labelloc=b //location of label in cluster subgraph
        node [shape=box]
        edge [fontcolor=blue, labelfloat=true];
        Beijing
        Nanjing -> Beijing [dir=both, label="JingHu Highway"]
        Shanghai -> Wuxi -> Nanjing  [dir=both, label="HuLin Highway"]
        
        { rank=max; Beijing}
        { rank=min; Nanjing, Wuxi}

    }
}

```

运行命令:   
>  $ dot -Tpng -o test.png test.dot -v   

可以得到下面的图样: 
![](/images/dot_1.png)

下面就几个关键的属性进行一下说明, E, N, G, S, C 分别代表属性是作用在  edges, nodes, the root graph, subgraphs, cluster subgraphs 上的.

Some attrubutes about **color**: 

- color: Basic drawing color for graphics, not text. For the latter, use the fontcolor attribute.  
  For edges, the value can either be a [single color](http://www.graphviz.org/doc/info/attrs.html#k:color) or a [colorList](http://www.graphviz.org/doc/info/attrs.html#k:colorList).
- bgcolor: When attached to the root graph, this color is used as the background for entire canvas. When a cluster attribute, it is used as the initial background for the cluster. If a cluster has a filled style, the cluster's fillcolor will overlay the background color.
- fontcolor: Color used for text.
- labelfontcolor: Color used for headlabel and taillabel. If not set, defaults to edge's fontcolor.
- fillcolor: Color used to fill the background of a node or cluster assuming style=filled, or a filled arrowhead. If fillcolor is not defined, color is used. (For clusters, if color is not defined, bgcolor is used.) If this is not defined, the default is used, except for shape=point or when the output format is MIF, which use black by default.
- pencolor: Color used to draw the bounding box around a cluster. If pencolor is not defined, color is used. If this is not defined, bgcolor is used. If this is not defined, the default is used. Note that a cluster inherits the root graph's attributes if defined. Thus, if the root graph has defined a pencolor, this will override a color or bgcolor attribute set for the cluster.

Some attrubutes about **rank**:

- rank: Rank constraints on the nodes in a subgraph.   
If rank="same", all nodes are placed on the same rank.   
If rank="min", all nodes are placed on the minimum rank.   
If rank="source", all nodes are placed on the minimum rank, and the only nodes on the minimum rank belong to some subgraph whose rank attribute is "source" or "min".   
Analogous criteria hold for rank="max" and rank="sink".   
Note: the minimum rank is topmost or leftmost, and the maximum rank is bottommost or rightmost.
- rankdir: Sets direction of graph layout. For example, if rankdir="LR", and barring cycles, an edge T -> H; will go from left to right. By default, graphs are laid out from top to bottom.  
This attribute also has a side-effect in determining how record nodes are interpreted. See record shapes.  
see [rankidr value list](http://www.graphviz.org/doc/info/attrs.html#k:rankdir) 
- ranksep: In dot, this gives the desired rank separation, in inches. This is the minimum vertical distance between the bottom of the nodes in one rank and the tops of nodes in the next. If the value contains "equally", the centers of all ranks are spaced equally apart. Note that both settings are possible, e.g., ranksep = "1.2 equally".  
  In twopi, this attribute specifies the radial separation of concentric circles. For twopi, ranksep can also be a list of doubles. The first double specifies the radius of the inner circle; the second double specifies the increase in radius from the first circle to the second; etc. If there are more circles than numbers, the last number is used as the increment for the remainder. 

Some attrubutes about **border and edge**:

- shape: Set the shape of a node.
- arrowhaed(E): Style of arrowhead on the head node of an edge. This will only appear if the dir attribute is "forward" or "both". See the [limitation](http://www.graphviz.org/doc/info/attrs.html#h:undir_note).
- arrowsize(E): Multiplicative scale factor for arrowheads.
- arrowtail(E): Style of arrowhead on the tail node of an edge. This will only appear if the dir attribute is "back" or "both". See the [limitation](http://www.graphviz.org/doc/info/attrs.html#h:undir_note).
- splines: Controls how, and if, edges are represented. If true, edges are drawn as splines routed around nodes; if false, edges are drawn as line segments. If set to none or "", no edges are drawn at all.
- style: Set style information for components of the graph. For cluster subgraphs, if style="filled", the cluster box's background is filled.  
  see detailed [style information](http://www.graphviz.org/doc/info/attrs.html#k:style)


Some attrubutes about **label**:

- label: Text label attached to objects. If a node's shape is record, then the label can have a special format which describes the record layout.
Note that a node's default label is "\N", so the node's name or ID becomes its label. Technically, a node's name can be an HTML string but this will not mean that the node's label will be interpreted as an HTML-like label. This is because the node's actual label is an ordinary string, which will be replaced by the raw bytes stored in the node's name. To get an HTML-like label, the label attribute value itself must be an HTML string.
- labelURL: If labelURL is defined, this is the link used for the label of an edge. This value overrides any URL defined for the edge.
- label_scheme: The value indicates whether to treat a node whose name has the form |edgelabel|* as a special node representing an edge label. The default (0) produces no effect. If the attribute is set to 1, sfdp uses a penalty-based method to make that kind of node close to the center of its neighbor. With a value of 2, sfdp uses a penalty-based method to make that kind of node close to the old center of its neighbor. Finally, a value of 3 invokes a two-step process of overlap removal and straightening.
- labelangle: This, along with labeldistance, determine where the headlabel (taillabel) are placed with respect to the head (tail) in polar coordinates. The origin in the coordinate system is the point where the edge touches the node. The ray of 0 degrees goes from the origin back along the edge, parallel to the edge at the origin.  
The angle, in degrees, specifies the rotation from the 0 degree ray, with positive angles moving counterclockwise and negative angles moving clockwise.
- labeldistance: Multiplicative scaling factor adjusting the distance that the headlabel(taillabel) is from the head(tail) node. The default distance is 10 points. See labelangle for more details.
- labelfloat: If true, allows edge labels to be less constrained in position. In particular, it may appear on top of other edges.
- labelfontcolor: Color used for headlabel and taillabel. If not set, defaults to edge's fontcolor.
- labelfontname: Font used for headlabel and taillabel. If not set, defaults to edge's fontname.
- labelfontsize: Font size, in points, used for headlabel and taillabel. If not set, defaults to edge's fontsize.
- labelhref: Synonym for labelURL.
- labeljust: Justification for cluster labels. If "r", the label is right-justified within bounding rectangle; if "l", left-justified; else the label is centered. Note that a subgraph inherits attributes from its parent. Thus, if the root graph sets labeljust to "l", the subgraph inherits this value.
- labelloc: Vertical placement of labels for nodes, root graphs and clusters.


### Pydot

pydot包提供了使用dot语言的python接口，可以方便地创建各种有向图和无向图。
官方网站：https://code.google.com/p/pydot/

安装依赖: pyparsing, Graphviz

```
$ apt-get install pyparsing
$ apt-get install graphviz
$ pip install pydot
```
在python shell中检查
> help(pydot.Dot)
> hep(pydot.Node)
> help(pydot.Edge)
> ...

编写描绘程序(test.py):

```
#!/usr/bin/python
# -*- coding: utf-8 -*-

import pydot
import os

#取得当前目录
BASE_DIR = os.path.split(os.path.realpath(__file__))[0]

#创建有向图  
g = pydot.Dot('G', graph_type='digraph', label='root graph',
            color='#FFFFFF', style='filled', clusterrank='local', 
            fontsize='12', compound='true', rankdir='TB', model='circuit')
             
#创建节点            
node = pydot.Node('1', label='Node_1', shape='box',
                 labelloc='b', overlap='false', fontsize='10',
                 image=BASE_DIR+"/node.png")
g.add_node(node)    #添加节点

#创建边
g.add_node(pydot.Node('B',label='Node_B'))
edge = pydot.Edge('1','B') 
g.add_edge(edge) #添加边

#创建聚类子图
zone = pydot.Subgraph('cluster_zone', graph_type='digraph', 
                    label='Zone A', color='blue', style='dotted')
zone.add_node(pydot.Node('z_1',label='zone_node_1'))
zone.add_node(pydot.Node('z_2',label='zone_node_2'))   
zone.add_edge(pydot.Edge('z_1','z_2'))                 
g.add_subgraph(zone) 
            
print g.to_string() # 打印图信息  

g.write_png("g.png") # 绘制图画

```

运行命令:   
>  $ python test.py

打印的图信息为:  

```
digraph AWS {
    label="root graph";
	rankdir=TB;
	style=filled;
	color="#FFFFFF";
	compound=true;
	fontsize=12;
	clusterrank=local;
	model=circuit;
	
	1 [labelloc=b, shape=box, fontsize=10, image="/path/to/node.png", 
	   overlap=false, label=Node_1];
	B [label=Node_B];
	1 -> B;
	
	subgraph cluster_zone {
		color=blue;
		style=dotted;
		label="Zone A";
		z_1 [label=zone_node_1];
		z_2 [label=zone_node_2];
		z_1 -> z_2;
	}

}
```

绘制出的图样:   
![](/images/dot_pydot.png)

### reference
这里有一些运用Dot或pydot画图的文章参考：  

[利用Graphviz 画结构图](http://www.cnblogs.com/sld666666/archive/2010/06/25/1765510.html)  
[Generating Graph Visualizations with pydot and Graphviz](http://pythonhaven.wordpress.com/2009/12/09/generating_graphs_with_pydot/)  
[Pydot Cluster Example](http://www.robertyu.com/wikiperdido/Pydot%20Clusters)  
