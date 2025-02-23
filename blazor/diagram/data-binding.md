---
layout: post
title: Data Binding in Blazor Diagram Component | Syncfusion
description: Learn here all about Data Binding such as local data, remote data in Syncfusion Blazor Diagram component and more.
platform: Blazor
control: Diagram Component
documentation: ug
---

# Data Binding in Blazor Diagram Component

* [Diagram](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.SfDiagramComponent.html) can be populated with the [Nodes](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.SfDiagramComponent.html#Syncfusion_Blazor_Diagram_SfDiagramComponent_Nodes) and [Connectors](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.SfDiagramComponent.html#Syncfusion_Blazor_Diagram_SfDiagramComponent_Connectors) based on the information provided from an external data source.

* Diagram exposes its specific data-related properties allowing you to specify the data source fields from where the node information has to be retrieved.

* The [DataSource](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html#Syncfusion_Blazor_Diagram_DataSourceSettings_DataSource) property is used to define the data source either as a collection of objects or as an instance of `DataSource` that needs to be populated in the diagram.

* The [ID](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html#Syncfusion_Blazor_Diagram_DataSourceSettings_ID) property is used to define the unique field of each JSON data.

* The [ParentID](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html#Syncfusion_Blazor_Diagram_DataSourceSettings_ParentID) property is used to define the parent field which builds the relationship between ID and parent field.

* The [Root](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html#Syncfusion_Blazor_Diagram_DataSourceSettings_Root) property is used to define the root node for the diagram populated from the data source.

* To explore those properties, see [DataSourceSettings](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html).

* Diagram supports two types of data binding. They are:

    1. Local data
    2. Remote data

## How to specify parent child relationship in Datasource

The [ID](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html#Syncfusion_Blazor_Diagram_DataSourceSettings_ID) and [ParentID](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html#Syncfusion_Blazor_Diagram_DataSourceSettings_ParentID) types are string in DataSourceSettings. When creating a parent-child relationship, it's essential to ensure that at least one node has an empty ParentID. This node will act as the root or parent node, with other nodes linked as its children.For example, let's consider the data source settings. Within the DataSource, we have defined three specific details. The DataModel class has attributes such as ID, ParentID, and various other details. In this scenario, the initial dataset does not have a parent ID, which means we have set it as empty. This node act as parent or root node of layout.Subsequent datasets, however, do contain a ParentID. Below is a code example illustrating this:

```csharp
@using Syncfusion.Blazor.Diagram

<SfDiagramComponent Height="600px" Nodes="@nodes" Connectors="@connectors" NodeCreating="@OnNodeCreating" ConnectorCreating="@OnConnectorCreating">
  <DataSourceSettings ID="Id" ParentID="ParentId" DataSource="DataSource"> </DataSourceSettings>
    <Layout Type="LayoutType.HierarchicalTree" HorizontalSpacing="@HorizontalSpacing" VerticalSpacing="@VerticalSpacing" >
    </Layout>
    <SnapSettings>
        <HorizontalGridLines LineColor="white" LineDashArray="2,2">
        </HorizontalGridLines>
        <VerticalGridLines LineColor="white" LineDashArray="2,2">
        </VerticalGridLines>
    </SnapSettings>
</SfDiagramComponent>

@code 
{
    int left = 40;
    int top = 50;
    DiagramObjectCollection<Node> nodes = new DiagramObjectCollection<Node>();
    DiagramObjectCollection<Connector> connectors = new DiagramObjectCollection<Connector>();
    int HorizontalSpacing = 40;
    int VerticalSpacing = 40;

    private void OnNodeCreating(IDiagramObject obj)
    {
        Node node = obj as Node;
        node.Height = 40;
        node.Width = 100;
        //Initializing the default node's shape style.
        node.Style = new ShapeStyle() { Fill = "darkcyan", StrokeWidth = 3, StrokeColor = "Black" };
    }

    private void OnConnectorCreating(IDiagramObject connector)
    {
        (connector as Connector).Type = ConnectorSegmentType.Orthogonal;
    }

    public class HierarchicalDetails
    {
        public string Id { get; set; }
        public string Role { get; set; }
        public string ParentId { get; set; }
        public string ChartType { get; set; }
        public string Color { get; set; }
    }
    public List<HierarchicalDetails> DataSource = new List<HierarchicalDetails>()
    {
        new HierarchicalDetails()   { Id= "parent", Role= "Board", Color= "#71AF17" },
        new HierarchicalDetails()   { Id= "1", Role= "General Manager", ParentId= "parent", ChartType= "right", Color= "#71AF17" },
        new HierarchicalDetails()   { Id= "11", Role= "Assistant Manager", ParentId= "1", Color= "#71AF17" },
    };
}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/Parent-Child%20Relationship)

## How to bind local data with diagram

Diagram can be populated based on the user defined JSON data (Local Data) by mapping the relevant data source fields.

To map the user defined JSON data with diagram, configure the fields of `DataSourceSettings`. The following code example illustrates how to bind local data with the diagram.

```cshtml
@using Syncfusion.Blazor.Diagram

<SfDiagramComponent @ref="@Diagram" 
                    Height="499px"
                    InteractionController="DiagramInteractions.ZoomPan" 
                    ConnectorCreating="@ConnectorDefaults" 
                    NodeCreating="@NodeDefaults">
    <DataSourceSettings ID="Name" ParentID="Category" DataSource="DataSource"/>
    <Layout @bind-Type="type" 
            @bind-HorizontalSpacing="@HorizontalSpacing" 
            @bind-Orientation="@orientation" 
            @bind-VerticalSpacing="@VerticalSpacing" 
            @bind-HorizontalAlignment="@horizontalAlignment" 
            @bind-VerticalAlignment="@verticalAlignment" 
            GetLayoutInfo="GetLayoutInfo">
        <LayoutMargin Top="50" Bottom="50" Right="50" Left="50"/>
    </Layout>
</SfDiagramComponent>

@code
{
    SfDiagramComponent Diagram;
    // Specify the layout type.
    LayoutType type = LayoutType.HierarchicalTree;
    // Specify the orientation of the layout.
    LayoutOrientation orientation = LayoutOrientation.TopToBottom;
    HorizontalAlignment horizontalAlignment = HorizontalAlignment.Auto;
    VerticalAlignment verticalAlignment = VerticalAlignment.Auto;
    int HorizontalSpacing = 30;
    int VerticalSpacing = 30;

    // Defines the connector's default values.
    private void ConnectorDefaults(IDiagramObject connector)
    {
        (connector as Connector).Type = ConnectorSegmentType.Orthogonal;
        (connector as Connector).TargetDecorator.Shape = DecoratorShape.None;
        (connector as Connector).Style = new ShapeStyle() { StrokeColor = "#6d6d6d" };
        (connector as Connector).Constraints = 0;
        (connector as Connector).CornerRadius = 5;
    }

    // Create the layout info.
    private TreeInfo GetLayoutInfo(IDiagramObject obj, TreeInfo options)
    {
        // Enable the sub-tree.
        options.EnableSubTree = true;
        // Specify the subtree orientation.
        options.Orientation = Orientation.Horizontal;
        return options;
    }

    // Defines the node's default values.
    private void NodeDefaults(IDiagramObject obj)
    {
        Node node = obj as Node;
        if (node.Data is System.Text.Json.JsonElement)
        {
            node.Data = System.Text.Json.JsonSerializer.Deserialize<HierarchicalDetails>(node.Data.ToString());
        }
        HierarchicalDetails hierarchicalData = node.Data as HierarchicalDetails;
        node.Style = new ShapeStyle() { Fill = "#659be5", StrokeColor = "none", StrokeWidth = 2, };
        node.BackgroundColor = "#659be5";
        node.Width = 150;
        node.Height = 50;
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>()
        {
            new ShapeAnnotation()
            {
                Content = hierarchicalData.Name,
                Style =new TextStyle(){Color = "white"}
            }
        };
    }

    // Create the hierarchical details with needed properties.
    public class HierarchicalDetails
    {
        public string Name { get; set; }
        public string FillColor { get; set; }
        public string Category { get; set; }
    }

    // Create the data source with node name and fill color values.
    public List<HierarchicalDetails> DataSource = new List<HierarchicalDetails>()
    {
        new HierarchicalDetails(){ Name ="Diagram", Category="",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Layout", Category="Diagram",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Tree layout", Category="Layout",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Organizational chart", Category="Layout",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Hierarchical tree", Category="Tree layout",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Radial tree", Category="Tree layout",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Mind map", Category="Hierarchical tree",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Family tree", Category="Hierarchical tree",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Management", Category="Organizational chart",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Human resources", Category="Management",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="University", Category="Management",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Business", Category="#Management",FillColor="#659be5"}
    };
}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/LocalData)

## How to bind expandoObject data with diagram

Diagram is a generic component which is strongly bound to a model type. There are cases when the model type is unknown during compile time. In such circumstances data can be bound to the Diagram as a list of **ExpandoObjects**. 
Diagram can also perform all kind of supported Layout operations in ExpandoObjects such as,
* Hierarchical layout
* Organization chart
* Mind Map layout
* Complex Hierarchical Tree layout
* Radial Tree Layout

The **ExpandoObject** can be bound to Diagram by assigning to the [DataSource] property of the [DataSourceSettings](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html).
The following code example illustrates how to bind ExpandoObject data with the diagram.


```cshtml
@using Syncfusion.Blazor.Diagram

<SfDiagramComponent @ref="@Diagram" 
                    Height="499px"
                    InteractionController="DiagramInteractions.ZoomPan" 
                    ConnectorCreating="@ConnectorDefaults" 
                    NodeCreating="@NodeDefaults">
    <DataSourceSettings ID="Name" ParentID="Category" DataSource="DataSource"/>
    <Layout @bind-Type="type" 
            @bind-HorizontalSpacing="@HorizontalSpacing" 
            @bind-Orientation="@orientation" 
            @bind-VerticalSpacing="@VerticalSpacing" 
            @bind-HorizontalAlignment="@horizontalAlignment" 
            @bind-VerticalAlignment="@verticalAlignment" 
            GetLayoutInfo="GetLayoutInfo">
        <LayoutMargin Top="50" Bottom="50" Right="50" Left="50"/>
    </Layout>
</SfDiagramComponent>

@code
{
    SfDiagramComponent Diagram;
    // Specify the layout type.
    LayoutType type = LayoutType.HierarchicalTree;
    // Specify the orientation of the layout.
    LayoutOrientation orientation = LayoutOrientation.TopToBottom;
    HorizontalAlignment horizontalAlignment = HorizontalAlignment.Auto;
    VerticalAlignment verticalAlignment = VerticalAlignment.Auto;
    int HorizontalSpacing = 30;
    int VerticalSpacing = 30;
    public List<ExpandoObject> DataSource { get; set; }
    public List<ExpandoObject> ExpandoData = new List<ExpandoObject>();
    public class ExpandoObject
    {   
        public string Name { get; set; }
        public string Category { get; set; }
        public string FillColor { get; set; }
    }
    // Defines the connector's default values.
    private void ConnectorDefaults(IDiagramObject connector)
    {
        (connector as Connector).Type = ConnectorSegmentType.Orthogonal;
        (connector as Connector).TargetDecorator.Shape = DecoratorShape.None;
        (connector as Connector).Style = new ShapeStyle() { StrokeColor = "#6d6d6d" };
        (connector as Connector).Constraints = 0;
        (connector as Connector).CornerRadius = 5;
    }

    // Create the layout info.
    private TreeInfo GetLayoutInfo(IDiagramObject obj, TreeInfo options)
    {
        // Enable the sub-tree.
        options.EnableSubTree = true;
        // Specify the subtree orientation.
        options.Orientation = Orientation.Horizontal;
        return options;
    }

    // Defines the node's default values.
    private void NodeDefaults(IDiagramObject obj)
    {
        Node node = obj as Node;
        if (node.Data is System.Text.Json.JsonElement)
        {
            node.Data = System.Text.Json.JsonSerializer.Deserialize<ExpandoObject>(node.Data.ToString());
        }
        dynamic hierarchicalData = node.Data as ExpandoObject;
        node.Style = new ShapeStyle() { Fill = "#659be5", StrokeColor = "none", StrokeWidth = 2, };
        node.BackgroundColor = "#659be5";
        node.Width = 150;
        node.Height = 50;
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>()
        {
            new ShapeAnnotation()
            {
                Content = hierarchicalData.Name,
                Style =new TextStyle(){Color = "white"}
            }
        };     
    }

     protected override void OnInitialized()
    {
        this.DataSource = GetData();
    }
    public List<ExpandoObject> GetData()
    {
        ExpandoData.Clear();
        dynamic Member1 = new ExpandoObject();

        Member1.Name = "Diagram";
        Member1.Category = "";
        Member1.FillColor = "#71AF17";
        ExpandoData.Add(Member1);

        dynamic Member2 = new ExpandoObject();

        Member2.Name = "Layout";
        Member2.Category = "Diagram";
        Member2.FillColor = "#659be5";
        ExpandoData.Add(Member2);

        dynamic Member3 = new ExpandoObject();

        Member3.Name = "Tree layout";
        Member3.Category = "Layout";
        Member3.FillColor = "#659be5";
        ExpandoData.Add(Member3);

        dynamic Member7 = new ExpandoObject();

        Member7.Name = "Organizational chart";
        Member7.Category = "Layout";
        Member7.FillColor = "#659be5";
        ExpandoData.Add(Member7);

        dynamic Member8 = new ExpandoObject();

        Member8.Name = "Hierarchical tree";
        Member8.Category = "Tree layout";
        Member8.FillColor = "#659be5";
        ExpandoData.Add(Member8);

        dynamic Member14 = new ExpandoObject();

        Member14.Name = "Radial tree";
        Member14.Category = "Tree layout";
        Member14.FillColor = "#659be5";
        ExpandoData.Add(Member14);

        dynamic Member9 = new ExpandoObject();

        Member9.Name = "Mind map";
        Member9.Category = "Hierarchical tree";
        Member9.FillColor = "#659be5";
        ExpandoData.Add(Member9);

        dynamic Member10 = new ExpandoObject();

        Member10.Name = "Family tree";
        Member10.Category = "Hierarchical tree";
        Member10.FillColor = "#659be5";
        ExpandoData.Add(Member10);

        dynamic Member4 = new ExpandoObject();

        Member4.Name = "Management";
        Member4.Category = "Organizational chart";
        Member4.FillColor = "#659be5";
        ExpandoData.Add(Member4);

        dynamic Member11 = new ExpandoObject();

        Member11.Name = "Human resources";
        Member11.Category = "Management";
        Member11.FillColor = "#659be5";
        ExpandoData.Add(Member11);

        dynamic Member12 = new ExpandoObject();

        Member12.Name = "University";
        Member12.Category = "Management";
        Member12.FillColor = "#659be5";
        ExpandoData.Add(Member12);

        return ExpandoData;
    }
}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/ExpandoObject)

##  How to bind dynamicObject data with diagram

Diagram is a generic component which is strongly bound to a model type. There are cases when the model type is unknown during compile time. In such circumstances data can be bound to the Diagram as a list of **DynamicObject**.
Diagram can also perform all kind of supported Layout operations in DynamicObject such as,
* Hierarchical layout
* Organization chart
* Mind Map layout
* Complex Hierarchical Tree layout
* Radial Tree layout

 The **DynamicObject** can be bound to Diagram by assigning to the [DataSource] property of the [DataSourceSettings](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html).
The following code example illustrates how to bind DynamicObject data with the diagram.

```cshtml
@using Syncfusion.Blazor.Diagram
@using System.Dynamic

<SfDiagramComponent @ref="@Diagram" 
                    Height="499px"
                    InteractionController="DiagramInteractions.ZoomPan" 
                    ConnectorCreating="@ConnectorDefaults" 
                    NodeCreating="@NodeDefaults">
    <DataSourceSettings ID="Name" ParentID="Category" DataSource="DataSource"/>
    <Layout @bind-Type="type" 
            @bind-HorizontalSpacing="@HorizontalSpacing" 
            @bind-Orientation="@orientation" 
            @bind-VerticalSpacing="@VerticalSpacing" 
            @bind-HorizontalAlignment="@horizontalAlignment" 
            @bind-VerticalAlignment="@verticalAlignment" 
            GetLayoutInfo="GetLayoutInfo">
        <LayoutMargin Top="50" Bottom="50" Right="50" Left="50"/>
    </Layout>
</SfDiagramComponent>

@code
{
    SfDiagramComponent Diagram;
    // Specify the layout type.
    LayoutType type = LayoutType.HierarchicalTree;
    // Specify the orientation of the layout.
    LayoutOrientation orientation = LayoutOrientation.TopToBottom;
    HorizontalAlignment horizontalAlignment = HorizontalAlignment.Auto;
    VerticalAlignment verticalAlignment = VerticalAlignment.Auto;
    int HorizontalSpacing = 30;
    int VerticalSpacing = 30;
    public List<HierarchicalDetails> DataSource { get; set; }
    public List<HierarchicalDetails> ExpandoData = new List<HierarchicalDetails>();

    // Defines the connector's default values.
    private void ConnectorDefaults(IDiagramObject connector)
    {
        (connector as Connector).Type = ConnectorSegmentType.Orthogonal;
        (connector as Connector).TargetDecorator.Shape = DecoratorShape.None;
        (connector as Connector).Style = new ShapeStyle() { StrokeColor = "#6d6d6d" };
        (connector as Connector).Constraints = 0;
        (connector as Connector).CornerRadius = 5;
    }

    // Create the layout info.
    private TreeInfo GetLayoutInfo(IDiagramObject obj, TreeInfo options)
    {
        // Enable the sub-tree.
        options.EnableSubTree = true;
        // Specify the subtree orientation.
        options.Orientation = Orientation.Horizontal;
        return options;
    }

    // Defines the node's default values.
    private void NodeDefaults(IDiagramObject obj)
    {
        Node node = obj as Node;
        if (node.Data is System.Text.Json.JsonElement)
        {
            node.Data = System.Text.Json.JsonSerializer.Deserialize<HierarchicalDetails>(node.Data.ToString());
        }
        dynamic hierarchicalData = node.Data as HierarchicalDetails;
        node.Style = new ShapeStyle() { Fill = "#659be5", StrokeColor = "none", StrokeWidth = 2, };
        node.BackgroundColor = "#659be5";
        node.Width = 150;
        node.Height = 50;
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>()
        {
            new ShapeAnnotation()
            {
                Content = hierarchicalData.Name,
                Style =new TextStyle(){Color = "white"}
            }
        };     
    }

    public class HierarchicalDetails:DynamicObject
    {
        Dictionary<string, object> dictionary = new Dictionary<string, object>();

        public override bool TryGetMember(GetMemberBinder binder, out object result)
        {
            string name = binder.Name;
            return dictionary.TryGetValue(name, out result);
        }
        public override bool TrySetMember(SetMemberBinder binder, object value)
        {
            dictionary[binder.Name] = value;
            return true;
        }

        public override System.Collections.Generic.IEnumerable<string> GetDynamicMemberNames()
        {
            return this.dictionary?.Keys;
        }

    }
    protected override void OnInitialized()
    {
        this.DataSource = GetData();
    }
    public List<HierarchicalDetails> GetData()
    {
        ExpandoData.Clear();
        dynamic Member1 = new HierarchicalDetails();

        Member1.Name = "Diagram";
        Member1.Category = "";
        Member1.FillColor = "#71AF17";
        ExpandoData.Add(Member1);

        dynamic Member2 = new HierarchicalDetails();

        Member2.Name = "Layout";
        Member2.Category = "Diagram";
        Member2.FillColor = "#659be5";
        ExpandoData.Add(Member2);

        dynamic Member3 = new HierarchicalDetails();

        Member3.Name = "Tree layout";
        Member3.Category = "Layout";
        Member3.FillColor = "#659be5";
        ExpandoData.Add(Member3);

        dynamic Member7 = new HierarchicalDetails();

        Member7.Name = "Organizational chart";
        Member7.Category = "Layout";
        Member7.FillColor = "#659be5";
        ExpandoData.Add(Member7);

        dynamic Member8 = new HierarchicalDetails();

        Member8.Name = "Hierarchical tree";
        Member8.Category = "Tree layout";
        Member8.FillColor = "#659be5";
        ExpandoData.Add(Member8);

        dynamic Member14 = new HierarchicalDetails();

        Member14.Name = "Radial tree";
        Member14.Category = "Tree layout";
        Member14.FillColor = "#659be5";
        ExpandoData.Add(Member14);

        dynamic Member9 = new HierarchicalDetails();

        Member9.Name = "Mind map";
        Member9.Category = "Hierarchical tree";
        Member9.FillColor = "#659be5";
        ExpandoData.Add(Member9);

        dynamic Member10 = new HierarchicalDetails();

        Member10.Name = "Family tree";
        Member10.Category = "Hierarchical tree";
        Member10.FillColor = "#659be5";
        ExpandoData.Add(Member10);

        dynamic Member4 = new HierarchicalDetails();

        Member4.Name = "Management";
        Member4.Category = "Organizational chart";
        Member4.FillColor = "#659be5";
        ExpandoData.Add(Member4);

        dynamic Member11 = new HierarchicalDetails();

        Member11.Name = "Human resources";
        Member11.Category = "Management";
        Member11.FillColor = "#659be5";
        ExpandoData.Add(Member11);

        dynamic Member12 = new HierarchicalDetails();

        Member12.Name = "University";
        Member12.Category = "Management";
        Member12.FillColor = "#659be5";
        ExpandoData.Add(Member12);

        return ExpandoData;
    }
}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/DynamicObj)

## How to bind json data with diagram

Local JSON data can be bound to the Diagram component by assigning the array of objects to the Json property of the SfDataManager component.

The following sample code demonstrates binding local data through the SfDataManager to the Diagram component,

```cshtml
@using Syncfusion.Blazor.Diagram
@using Syncfusion.Blazor.Data

<SfDiagramComponent @ref="Diagram" Width="1000px" Height="500px"
                    NodeCreating="NodeDefaults" SetNodeTemplate="SetTemplate">
    <DataSourceSettings ID="Name" ParentID="Category">
        <SfDataManager Json="DataSource" Adaptor="Syncfusion.Blazor.Adaptors.JsonAdaptor"></SfDataManager>
    </DataSourceSettings>
    <Layout HorizontalSpacing="40" VerticalSpacing="40" Type="LayoutType.HierarchicalTree"></Layout>
</SfDiagramComponent>

@code
{
    SfDiagramComponent Diagram;
    float x = 100;
    float y = 100;
    Query Query = new Query().Select(new List<string>() { "EmployeeID", "ReportsTo", "FirstName" }).Take(9);

    // Create the hierarchical details with needed properties.
    public class HierarchicalDetails
    {
        public string Name { get; set; }
        public string FillColor { get; set; }
        public string Category { get; set; }
    }

    // Create the data source with node name and fill color values.
    public HierarchicalDetails[] DataSource = new HierarchicalDetails[]
    {
        new HierarchicalDetails(){ Name ="Diagram", Category="",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Layout", Category="Diagram",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Organizational chart", Category="Diagram",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Tree layout", Category="Layout",FillColor="#659be5"},
        new HierarchicalDetails(){ Name ="Hierarchical tree", Category="Tree layout",FillColor="#659be5"},
    };
    
    // Defines the node's default values.
    private void NodeDefaults(IDiagramObject obj)
    {
        Node node = obj as Node;
        node.OffsetX = x;
        node.OffsetY = y;
        x += 100;
        HierarchicalDetails hierarchicalData = node.Data as HierarchicalDetails;
        node.Style.Fill = hierarchicalData.FillColor;
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>()
        {
            new ShapeAnnotation()
            {
                Content = hierarchicalData.Name
            }
        };
    }

    private CommonElement SetTemplate(IDiagramObject node)
    {
        return null;
    }
}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/JSONData)

## How to bind remote data with diagram

To bind remote data to [Diagram component](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.SfDiagramComponent.html), assign service data as an instance of [SfDataManager](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Data.SfDataManager.html) to the [DataSource](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Diagram.DataSourceSettings.html#Syncfusion_Blazor_Diagram_DataSourceSettings_DataSource) property or by using SfDataManager component. To interact with remote data source, provide the endpoint Url.

When using SfDataManager for data binding then the TValue must be provided explicitly in the diagram component. By default, SfDataManager uses ODataAdaptor for remote data-binding.

### Binding with OData services

OData (Open Data Protocol) is a standardized protocol for building and consuming RESTful APIs. It enables the creation and consumption of queryable and interoperable REST APIs in a simple and standard way. OData services expose data as resources that are addressable by URLs and can be queried using standard HTTP methods (GET, POST, PUT, DELETE). For more details on OData services, refer to the [OData documentation](https://www.odata.org/documentation/odata-version-3-0/odata-version-3-0-core-protocol/). To bind an OData service, use the ODataAdaptor.

```cshtml
@using Syncfusion.Blazor.Diagram
@using Syncfusion.Blazor.Data
@inject HttpClient Http


<div id="diagram-space" class="content-wrapper">
    <SfDiagramComponent Height="400px" NodeCreating="OnNodeCreating" ConnectorCreating="OnConnectorCreating">
        <DataSourceSettings Id="EmployeeID" ParentId="ReportsTo">
            <SfDataManager Url="https://blazor.syncfusion.com/services/production/odata/DiagramOData" Adaptor="Syncfusion.Blazor.Adaptors.ODataV4Adaptor"></SfDataManager>
        </DataSourceSettings>
        <SnapSettings Constraints="SnapConstraints.None"></SnapSettings>
        <Layout HorizontalSpacing="100" VerticalSpacing="100" Type="LayoutType.HierarchicalTree"></Layout>
    </SfDiagramComponent>
</div>

@code {
    SfDiagramComponent Diagrams;
    private float x = 100;
    private float y = 100;

    public class Employee {
        public int? EmployeeID { get; set; }
        public string FirstName { get; set; }
        public int? ReportsTo { get; set; }
    }

    private Query Query = new Query().Select(new List<string>() { "EmployeeID", "ReportsTo", "FirstName" }).Take(9);

    private void OnNodeCreating(IDiagramObject obj) {
        Node node = obj as Node;
        node.OffsetX = x;
        node.OffsetY = y;
        node.Width = 80;
        node.Height = 40;
        node.Shape = new BasicShape() { Type = Syncfusion.Blazor.Diagram.NodeShapes.Basic, Shape = NodeBasicShapes.Rectangle, CornerRadius = 8 };
        node.Style = new ShapeStyle() { StrokeWidth = 0, Fill = "" };
        x += 100;

        Dictionary<string, object> data = node.Data as Dictionary<string, object>;
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>() {
            new ShapeAnnotation() {
                Content = data["FirstName"].ToString(),
                Style = new TextStyle() { Color = "white" }
            }
        };

        switch (data["FirstName"].ToString()) {
            case "Andrew":
                node.Style.Fill = "#3A4857";
                break;
            case "Nancy":
                node.Style.Fill = "#2B8C68";
                break;
            case "Janet":
                node.Style.Fill = "#488CC1";
                break;
            case "Margaret":
                node.Style.Fill = "#4C888F";
                break;
            case "Steven":
                node.Style.Fill = "#8E4DB4";
                break;
            case "Laura":
                node.Style.Fill = "#CD6A32";
                break;
            default:
                node.Style.Fill = "#8E4DB4";
                break;
        }
    }

    private void OnConnectorCreating(IDiagramObject obj) {
        Connector connector = obj as Connector;
        connector.Style.StrokeColor = "#048785";
        connector.Type = ConnectorSegmentType.Orthogonal;
        connector.TargetDecorator.Shape = DecoratorShape.None;
        connector.SourceDecorator.Shape = DecoratorShape.None;
        connector.Style = new ShapeStyle() { StrokeColor = "#3A4857", Fill = "#3A4857", StrokeWidth = 1, StrokeDashArray = "3,3" };
    }

}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/RemoteData/ODataAdaptor)

### Binding with OData v4 services

The ODataV4 is an improved version of OData protocols, and the SfDataManager can also retrieve and consume OData v4 services. For more details on OData v4 services, refer to the [OData documentation](http://docs.oasis-open.org/odata/odata/v4.0/errata03/os/complete/part1-protocol/odata-v4.0-errata03-os-part1-protocol-complete.html#_Toc453752197). To bind OData v4 service, use the ODataV4Adaptor.

```cshtml
@using Syncfusion.Blazor.Diagram
@using Syncfusion.Blazor.Data
@inject HttpClient Http


<div id="diagram-space" class="content-wrapper">
    <SfDiagramComponent Height="400px" NodeCreating="OnNodeCreating" ConnectorCreating="OnConnectorCreating">
        <DataSourceSettings Id="EmployeeID" ParentId="ReportsTo">
            <SfDataManager Url="https://blazor.syncfusion.com/services/production/odata/DiagramODataV4" Adaptor="Syncfusion.Blazor.Adaptors.ODataV4Adaptor"></SfDataManager>
        </DataSourceSettings>
        <SnapSettings Constraints="SnapConstraints.None"></SnapSettings>
        <Layout HorizontalSpacing="100" VerticalSpacing="100" Type="LayoutType.HierarchicalTree"></Layout>
    </SfDiagramComponent>
</div>

@code {
    SfDiagramComponent Diagrams;
    private float x = 100;
    private float y = 100;

    public class Employee {
        public int? EmployeeID { get; set; }
        public string FirstName { get; set; }
        public int? ReportsTo { get; set; }
    }

    private Query Query = new Query().Select(new List<string>() { "EmployeeID", "ReportsTo", "FirstName" }).Take(9);

    private void OnNodeCreating(IDiagramObject obj) {
        Node node = obj as Node;
        node.OffsetX = x;
        node.OffsetY = y;
        node.Width = 80;
        node.Height = 40;
        node.Shape = new BasicShape() { Type = Syncfusion.Blazor.Diagram.NodeShapes.Basic, Shape = NodeBasicShapes.Rectangle, CornerRadius = 8 };
        node.Style = new ShapeStyle() { StrokeWidth = 0, Fill = "" };
        x += 100;

        Dictionary<string, object> data = node.Data as Dictionary<string, object>;
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>() {
            new ShapeAnnotation() {
                Content = data["FirstName"].ToString(),
                Style = new TextStyle() { Color = "white" }
            }
        };

        switch (data["FirstName"].ToString()) {
            case "Andrew":
                node.Style.Fill = "#3A4857";
                break;
            case "Nancy":
                node.Style.Fill = "#2B8C68";
                break;
            case "Janet":
                node.Style.Fill = "#488CC1";
                break;
            case "Margaret":
                node.Style.Fill = "#4C888F";
                break;
            case "Steven":
                node.Style.Fill = "#8E4DB4";
                break;
            case "Laura":
                node.Style.Fill = "#CD6A32";
                break;
            default:
                node.Style.Fill = "#8E4DB4";
                break;
        }
    }

    private void OnConnectorCreating(IDiagramObject obj) {
        Connector connector = obj as Connector;
        connector.Style.StrokeColor = "#048785";
        connector.Type = ConnectorSegmentType.Orthogonal;
        connector.TargetDecorator.Shape = DecoratorShape.None;
        connector.SourceDecorator.Shape = DecoratorShape.None;
        connector.Style = new ShapeStyle() { StrokeColor = "#3A4857", Fill = "#3A4857", StrokeWidth = 1, StrokeDashArray = "3,3" };
    }

}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/RemoteData/ODataV4Adaptor)

### Binding with WebAPI services

You can use the `WebApiAdaptor` to interact with Web APIs created with OData endpoints. The `WebApiAdaptor` is extended from the `ODataAdaptor`. Therefore, to use the `WebApiAdaptor`, the endpoint must understand the OData formatted queries sent with the request.

To enable OData query option for a Web API, refer to this [documentation](https://learn.microsoft.com/en-us/aspnet/web-api/overview/odata-support-in-aspnet-web-api/supporting-odata-query-options).

The following sample code demonstrates hoe to bind remote data to the Diagram component through the [SfDataManager](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Data.SfDataManager.html) using a Web API service,

```cshtml
@using Syncfusion.Blazor.Diagram
@using Syncfusion.Blazor.Data
@inject HttpClient Http


<div id="diagram-space" class="content-wrapper">
    <SfDiagramComponent Height="400px" NodeCreating="OnNodeCreating" ConnectorCreating="OnConnectorCreating">
        <DataSourceSettings Id="EmployeeID" ParentId="ReportsTo">
            <SfDataManager Url="https://blazor.syncfusion.com/services/production/api/EmployeeDetails" Adaptor="Syncfusion.Blazor.Adaptors.WebApiAdaptor"></SfDataManager>
        </DataSourceSettings>
        <SnapSettings Constraints="SnapConstraints.None"></SnapSettings>
        <Layout HorizontalSpacing="100" VerticalSpacing="100" Type="LayoutType.HierarchicalTree"></Layout>
    </SfDiagramComponent>
</div>

@code {
    SfDiagramComponent Diagrams;
    private float x = 100;
    private float y = 100;

    public class Employee {
        public int? EmployeeID { get; set; }
        public string FirstName { get; set; }
        public int? ReportsTo { get; set; }
    }

    private Query Query = new Query().Select(new List<string>() { "EmployeeID", "ReportsTo", "FirstName" }).Take(9);

    private void OnNodeCreating(IDiagramObject obj) {
        Node node = obj as Node;
        node.OffsetX = x;
        node.OffsetY = y;
        node.Width = 80;
        node.Height = 40;
        node.Shape = new BasicShape() { Type = Syncfusion.Blazor.Diagram.NodeShapes.Basic, Shape = NodeBasicShapes.Rectangle, CornerRadius = 8 };
        node.Style = new ShapeStyle() { StrokeWidth = 0, Fill = "" };
        x += 100;

        Dictionary<string, object> data = node.Data as Dictionary<string, object>;
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>() {
            new ShapeAnnotation() {
                Content = data["FirstName"].ToString(),
                Style = new TextStyle() { Color = "white" }
            }
        };

        switch (data["FirstName"].ToString()) {
            case "Andrew":
                node.Style.Fill = "#3A4857";
                break;
            case "Nancy":
                node.Style.Fill = "#2B8C68";
                break;
            case "Janet":
                node.Style.Fill = "#488CC1";
                break;
            case "Margaret":
                node.Style.Fill = "#4C888F";
                break;
            case "Steven":
                node.Style.Fill = "#8E4DB4";
                break;
            case "Laura":
                node.Style.Fill = "#CD6A32";
                break;
            default:
                node.Style.Fill = "#8E4DB4";
                break;
        }
    }

    private void OnConnectorCreating(IDiagramObject obj) {
        Connector connector = obj as Connector;
        connector.Style.StrokeColor = "#048785";
        connector.Type = ConnectorSegmentType.Orthogonal;
        connector.TargetDecorator.Shape = DecoratorShape.None;
        connector.SourceDecorator.Shape = DecoratorShape.None;
        connector.Style = new ShapeStyle() { StrokeColor = "#3A4857", Fill = "#3A4857", StrokeWidth = 1, StrokeDashArray = "3,3" };
    }

}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/RemoteData/WebAPIAdaptor)

### Binding with URL services
A URL service is a web service accessible via a Uniform Resource Locator (URL). These services provide specific functionalities or data when a client sends an HTTP request to the given URL. URL services are a foundational aspect of web-based APIs (Application Programming Interfaces) and RESTful services. The [UrlAdaptor](../data/adaptors#url-adaptor) serves as the base adaptor for interacting with remote data services. Most built-in adaptors are derived from the `UrlAdaptor`.

The following sample code demonstrates how to bind data to the Diagram component through the [SfDataManager](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Data.SfDataManager.html) using the `UrlAdaptor`,

```cshtml
@using Syncfusion.Blazor.Diagram
@using Syncfusion.Blazor.Data
@inject HttpClient Http


<div id="diagram-space" class="content-wrapper">
    <SfDiagramComponent Height="400px" NodeCreating="OnNodeCreating" ConnectorCreating="OnConnectorCreating">
        <DataSourceSettings Id="EmployeeID" ParentId="ReportsTo">
            <SfDataManager Url="https://blazor.syncfusion.com/services/production/api/DiagramRemoteAPI" Adaptor="Syncfusion.Blazor.Adaptors.UrlAdaptor"></SfDataManager>
        </DataSourceSettings>
        <SnapSettings Constraints="SnapConstraints.None"></SnapSettings>
        <Layout HorizontalSpacing="100" VerticalSpacing="100" Type="LayoutType.HierarchicalTree"></Layout>
    </SfDiagramComponent>
</div>

@code {
    SfDiagramComponent Diagrams;
    private float x = 100;
    private float y = 100;

    public class Employee {
        public int? EmployeeID { get; set; }
        public string FirstName { get; set; }
        public int? ReportsTo { get; set; }
    }

    private Query Query = new Query().Select(new List<string>() { "EmployeeID", "ReportsTo", "FirstName" }).Take(9);

    private void OnNodeCreating(IDiagramObject obj) {
        Node node = obj as Node;
        node.OffsetX = x;
        node.OffsetY = y;
        node.Width = 80;
        node.Height = 40;
        node.Shape = new BasicShape() { Type = Syncfusion.Blazor.Diagram.NodeShapes.Basic, Shape = NodeBasicShapes.Rectangle, CornerRadius = 8 };
        node.Style = new ShapeStyle() { StrokeWidth = 0, Fill = "" };
        x += 100;

        Dictionary<string, object> data = node.Data as Dictionary<string, object>;
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>() {
            new ShapeAnnotation() {
                Content = data["FirstName"].ToString(),
                Style = new TextStyle() { Color = "white" }
            }
        };

        switch (data["FirstName"].ToString()) {
            case "Andrew":
                node.Style.Fill = "#3A4857";
                break;
            case "Nancy":
                node.Style.Fill = "#2B8C68";
                break;
            case "Janet":
                node.Style.Fill = "#488CC1";
                break;
            case "Margaret":
                node.Style.Fill = "#4C888F";
                break;
            case "Steven":
                node.Style.Fill = "#8E4DB4";
                break;
            case "Laura":
                node.Style.Fill = "#CD6A32";
                break;
            default:
                node.Style.Fill = "#8E4DB4";
                break;
        }
    }

    private void OnConnectorCreating(IDiagramObject obj) {
        Connector connector = obj as Connector;
        connector.Style.StrokeColor = "#048785";
        connector.Type = ConnectorSegmentType.Orthogonal;
        connector.TargetDecorator.Shape = DecoratorShape.None;
        connector.SourceDecorator.Shape = DecoratorShape.None;
        connector.Style = new ShapeStyle() { StrokeColor = "#3A4857", Fill = "#3A4857", StrokeWidth = 1, StrokeDashArray = "3,3" };
    }

}
```
You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/RemoteData/URLAdaptor)

### Binding with GraphQL service
GraphQL is a query language for APIs with which you can get exactly what you need and nothing more. The GraphQLAdaptor provides an option to retrieve data from the GraphQL server. For more details on GraphQL service, refer to the [GraphQL documentation](../data/adaptors#graphql-service-binding).

```cshtml
@using Syncfusion.Blazor.Diagram
@using Syncfusion.Blazor.Data
@using System.Collections.ObjectModel
@using Syncfusion.Blazor.Grids
@using Syncfusion.Blazor

<div class="content-wrapper" style="width:100%">
    <div>
        <SfDiagramComponent @ref="@diagram"  Height="400px" InteractionController="@DiagramInteractions.ZoomPan" 
                            NodeCreating="OnNodeCreating" ConnectorCreating="OnConnectorCreating" >
            <DataSourceSettings Id="OrderID" ParentId="CustomerID">
            <SfDataManager Url="https://localhost:7131/graphql" GraphQLAdaptorOptions=@adaptorOptions Adaptor="Adaptors.GraphQLAdaptor"></SfDataManager>
            </DataSourceSettings>
            <SnapSettings Constraints ="SnapConstraints.ShowLines"></SnapSettings>
            <Layout HorizontalSpacing="40" VerticalSpacing="40" Type="LayoutType.HierarchicalTree"></Layout>
        </SfDiagramComponent>
    </div>
</div>
@code{
    public SfDiagramComponent diagram;
    private float x = 100;
    private float y = 100;
    private GraphQLAdaptorOptions adaptorOptions { get; set; } = new GraphQLAdaptorOptions
        {
            Query = @"
            query ordersData($dataManager: DataManagerRequestInput!){
                ordersData(dataManager: $dataManager) {
                    count, result { OrderID, CustomerID, EmployeeID, } , aggregates
                }
            }",
            ResolverName = "OrdersData"
        };

    public class Order
    {
        public int? OrderID { get; set; }
        public string CustomerID { get; set; }
        public string EmployeeID { get; set; } 
    }
    private void OnNodeCreating(IDiagramObject obj)
    {
        Node node = obj as Node;

        node.Width = 80;
        node.Height = 45;        
        node.Shape = new BasicShape() { Type = Syncfusion.Blazor.Diagram.NodeShapes.Basic, Shape = NodeBasicShapes.Rectangle };
        node.Style = new ShapeStyle() { StrokeWidth = 0, Fill = "#2084c4" };
        Dictionary<string, object> data = node.Data as Dictionary<string, object>;
        string content = "";
        if (data != null)
        {
            foreach (var kvp in data)
            {
                if (kvp.Key == "EmployeeID")
                {
                    content = kvp.Value.ToString(); 
                }
            }
        }
        node.Annotations = new DiagramObjectCollection<ShapeAnnotation>()
            {
                // Annotation is created and stored in the Annotations collection of Node.
                new ShapeAnnotation 
                {
                    Content = content,
                    Style = new TextStyle() 
                    {
                        Color = "White",
                        Bold = true,
                        FontSize = 12, 
                        FontFamily = "TimesNewRoman"
                    }
                }
            };
      
    }
    @*End:Hidden*@
    private void OnConnectorCreating(IDiagramObject obj)
    {
        Connector connector = obj as Connector;
        connector.Style.StrokeColor = "#048785";
        connector.Type = ConnectorSegmentType.Orthogonal;
        connector.TargetDecorator.Shape = DecoratorShape.None;
        connector.SourceDecorator.Shape = DecoratorShape.None;
        connector.Style = new ShapeStyle() { StrokeColor = "#3A4857", Fill = "#3A4857" };
    }
}
```

![Binding with GraphQL service](images/GraphQLAdaptor.png) 

You can download a complete working sample from [GitHub](https://github.com/SyncfusionExamples/Blazor-Diagram-Examples/tree/master/UG-Samples/DataBinding/GraphQLAdaptor)


## See Also

* [How to arrange the diagram nodes and connectors using varies layout](./layout/automatic-layout)
