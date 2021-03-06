core_topology_vertex_filleting.py
=================================

Abstract
^^^^^^^^

No available documentation script.


------

Launch the example
^^^^^^^^^^^^^^^^^^

  $ python core_topology_vertex_filleting.py

------


Code
^^^^


.. code-block:: python

  ##along with pythonOCC.  If not, see <http://www.gnu.org/licenses/>.
  
  # A sample that shows how to generate the gear geometry according
  # to knowledge
  
  from OCC.BRepFilletAPI import BRepFilletAPI_MakeFillet
  from OCC.BRep import BRep_Tool
  from OCC.BRepPrimAPI import BRepPrimAPI_MakeBox
  from OCC.TopExp import (TopExp_Explorer,
                          topexp_MapShapesAndAncestors,
                          topexp_FirstVertex,
                          topexp_LastVertex)
  from OCC.TopAbs import TopAbs_VERTEX, TopAbs_EDGE
  from OCC.TopTools import (TopTools_IndexedDataMapOfShapeListOfShape,
                            TopTools_ListIteratorOfListOfShape)
  from OCC.TopoDS import topods_Vertex, topods_Edge
  
  from OCC.Display.SimpleGui import init_display
  display, start_display, add_menu, add_function_to_menu = init_display()
  
  # create shape
  cube = BRepPrimAPI_MakeBox(100, 100, 100).Shape()
  
  topExp = TopExp_Explorer()
  topExp.Init(cube, TopAbs_VERTEX)
  # get two vertices
  vertA = topods_Vertex(topExp.Current())
  topExp.Next()
  vertB = topods_Vertex(topExp.Current())
  
  
  def vertex_fillet(cube_shp, vert):
      # apply a fillet on incident edges on a vertex
      afillet = BRepFilletAPI_MakeFillet(cube_shp)
      cnt = 0
      # find edges from vertex
      _map = TopTools_IndexedDataMapOfShapeListOfShape()
      topexp_MapShapesAndAncestors(cube_shp, TopAbs_VERTEX, TopAbs_EDGE, _map)
      results = _map.FindFromKey(vert)
      topology_iterator = TopTools_ListIteratorOfListOfShape(results)
      while topology_iterator.More():
          edge = topods_Edge(topology_iterator.Value())
          topology_iterator.Next()
          first, last = topexp_FirstVertex(edge), topexp_LastVertex(edge)
          vertex, first_vert, last_vert = BRep_Tool().Pnt(vert), BRep_Tool().Pnt(first), BRep_Tool().Pnt(last)
          if edge.Orientation():
              if not vertex.IsEqual(first_vert, 0.001):
                  afillet.Add(0, 20., edge)
              else:
                  afillet.Add(20, 0, edge)
          cnt += 1
      afillet.Build()
      if afillet.IsDone():
          return afillet.Shape()
      else:
          raise AssertionError('you failed on me you fool!')
  
  filleted_vertA = vertex_fillet(cube, vertA)
  
  display.DisplayShape(filleted_vertA, update=True)
  start_display()

Screenshots
^^^^^^^^^^^


  .. image:: images/screenshots/capture-core_topology_vertex_filleting-1-1511702297.jpeg

