#CesiumMeshVisualizer 
<a href="https://mikeswei.github.io/CesiumMeshVisualizer/Document/index.html" target="_blank">Document</a>
    <hr />
    demos:<br />
    <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/CSG/index.html">
           <img src="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/CSG/screenshot.jpg" /> 
    </a><br />
    <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/CSG/index.html">CSG</a><br /><br />
    <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/LOD/index.html">
       <img src="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/LOD/screenshot.jpg" /> 
    </a><br />
    <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/LOD/index.html">LOD</a><br /><br />
   <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/MeshVisualizer/index.html">MeshVisualizer</a><br /><br />
<a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/physics/helloworld.html">physics/helloworld</a><br/><br />
  <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/physics/cloth.html">
        <img src="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/physics/cloth.jpg" />
     </a><br /> 
    <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/physics/cloth.html">physics/cloth</a><br /><br />
    <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/physics/vehicle.html">
       <img src="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/physics/vehicle.jpg" />
    </a><br /> 
   <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/physics/vehicle.html">physics/vehicle</a><br /><br />
   <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/ReferenceMesh/index.html">ReferenceMesh</a><br /><br />
   <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/VolumeRendering/index.html">
     <img src="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/VolumeRendering/screenshot.jpg" /> 
   </a><br />
 <a target="_blank" href="https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/VolumeRendering/index.html">VolumeRendering</a><br />

Example<br/>


        MeshVisualizer = Cesium.MeshVisualizer;
        Mesh = Cesium.Mesh;
        MeshMaterial = Cesium.MeshMaterial; 
        FramebufferTexture = Cesium.FramebufferTexture;

        var center = Cesium.Cartesian3.fromDegrees(homePosition[0], homePosition[1], 50000);
        var modelMatrix = Cesium.Transforms.eastNorthUpToFixedFrame(center);

        var meshVisualizer = new MeshVisualizer({
            modelMatrix: modelMatrix,
        });
        viewer.scene.primitives.add(meshVisualizer);


        //示例1：Cesium.Geometry+Cesium.MeshMaterial组合
        var box = Cesium.BoxGeometry.createGeometry(Cesium.BoxGeometry.fromDimensions({
            dimensions: new Cesium.Cartesian3(100000, 50000, 50000),
            vertexFormat: Cesium.VertexFormat.POSITION_ONLY
        }));
        
        var material = new MeshMaterial({
            defaultColor: "rgba(255,0,0,1.0)",
            wireframe: false,
            side: MeshMaterial.Sides.DOUBLE
        });
        var boxMesh = new Mesh(box, material);

        meshVisualizer.add(boxMesh);

        //示例2：Cesium.CSG+Cesium.MeshMaterial组合，可以用Cesium.CSG做布尔运算并渲染运算结果

        //首先使用Cesium创建球体
         var sphere = new Cesium.SphereGeometry({
             radius: 50000.0,
             vertexFormat: Cesium.VertexFormat.POSITION_ONLY
         });
         sphere = Cesium.SphereGeometry.createGeometry(sphere);
        
         var sphereMesh = new Mesh(sphere, material);
         sphereMesh.position = new Cesium.Cartesian3(100000, 0, 0)
         meshVisualizer.add(sphereMesh);

         //将球体对象Cesium.SphereGeometry转成Cesium.CSG实例
         sphere = CSG.toCSG(sphere);
         //将盒子对象转成Cesium.CSG实例
         box = CSG.toCSG(box);

          //做布尔运算
          var subResult = sphere.subtract(box);
          //渲染运算结果
          var subResultMesh = new Mesh(subResult, material);
          subResultMesh.position = new Cesium.Cartesian3(700000, 0, 0)
          meshVisualizer.add(subResultMesh);

          //示例3：使用帧缓存作纹理,实际应用中如体绘制，风场流场绘制等等都可以运用此技术

          function createGeometry() {
            var p1 = new Cesium.Cartesian3(-50000, 50000, 100);
            var p2 = new Cesium.Cartesian3(-50000, -50000, 100);
            var p3 = new Cesium.Cartesian3(50000, -50000, 100);
            var p4 = new Cesium.Cartesian3(50000, 50000, 100);

            var positions = new Float64Array([
              p1.x, p1.y, p1.z,
              p2.x, p2.y, p2.z,
              p3.x, p3.y, p3.z,
              p4.x, p4.y, p4.z
            ]);
            var indices = new Uint16Array([
              0, 1, 3,
              1, 2, 3,
            ]);
            var sts = new Float32Array([
              1, 1,
              1, 0,
              0, 0,
              0, 1
            ]);
            var geometry = new Cesium.Geometry({
                attributes: {
                    position: new Cesium.GeometryAttribute({
                        componentDatatype: Cesium.ComponentDatatype.DOUBLE,
                        componentsPerAttribute: 3,
                        values: positions
                    }),
                    st: new Cesium.GeometryAttribute({
                        componentDatatype: Cesium.ComponentDatatype.FLOAT,
                        componentsPerAttribute: 2,
                        values: sts
                    })
                },
                indices: indices,
                primitiveType: Cesium.PrimitiveType.TRIANGLES,
                boundingSphere: Cesium.BoundingSphere.fromVertices(positions)
            });

            return geometry;
        }
        //将上文中的盒子渲染到缓存，作为纹理参与createGeometry（）方法创建的几何体渲染过程
        var framebufferTex = new FramebufferTexture(boxMesh);
        var geometry = createGeometry();
        var customMesh = new Mesh(geometry, new MeshMaterial({

            uniforms: {
                u_textureMap: framebufferTex//Cesium.buildModuleUrl('Widgets/Images/TerrainProviders/STK.png')
            },
            side: MeshMaterial.Sides.DOUBLE,
            vertexShader : "\n\
                \n\
                varying vec3 v_position;\n\
                varying vec2 v_st;\n\
                \n\
                void main(void) \n\
                {\n\
                vec4 pos = u_modelViewMatrix * vec4(position,1.0);\n\
                v_position = pos.xyz;\n\
                v_st=st;\n\
                gl_Position = u_projectionMatrix * pos;\n\
                }",
            fragmentShader : "varying vec2 v_st;\
                uniform sampler2D u_textureMap;\
                void main()\
                {\
                gl_FragColor = texture2D(u_textureMap,v_st);\n\
                \
                }\
                "
        }));
        customMesh.position = new Cesium.Cartesian3(100000, 0, 0);
        meshVisualizer.add(customMesh);

 
    
携源码示例和文档，诚意奉献，https://github.com/MikesWei/CesiumMeshVisualizer ， 名字不重要。 <br/>
    个人觉得Threejs里的Mesh、Geometry、Material极好，尤其是各个属性可以独立地、动态地编辑的特点，是目前在Cesium的Primitive中没有充分体现的。比如
你渲染一个立方体，想要旋转，竟然还需要自己去算新的ModelMatrix；再比如画个波动的水面竟然不能只修改顶点位置属性，修改完顶点位置后竟然需要移除旧的
Primitive再创建一个。。。也许我没找到接口，但是真心难找啊！还好，稍微扒过Cesium Primitive的都知道，再往稍微底层一点的还有DrawCommand可以灵活使用，
但是也太灵活，代码量大，还容易出错，所以决定自己模仿Threejs的Mesh、Geometry、Material并实现一个负责渲染和管理它们的更支持动态管理可渲染对象的
Primitive；之前的思路是直接转换Threejs的Mesh，产物就是[Cesium3js](https://github.com/MikesWei/cesium3js)，但是名字太大，误以为是要完全整合Cesium和Threejs，这与我本意不符,遂重新整理优化：
<br/>
* 1、不直接支持threejs的mesh，但是支持Threejs的Geometry和BufferGeometry来构建几何体。内置了Threejs Geometry、BufferGeometry到Cesium.Geometry的转换接口，同时提供Cesium.Geometry到Threejs BufferGeometry的转换接口。这些接口由[GeometryUtils](https://mikeswei.github.io/CesiumMeshVisualizer/Document/Cesium.GeometryUtils.html)类提供。
* 2、定义Mesh，支持Cesium.Geometry、THREE.Geometry、THREE.BufferGeometry以及[CSG](https://mikeswei.github.io/CesiumMeshVisualizer/Document/Cesium.CSG.html)运算结果模型。
* 3、更方便的动态渲染和管理。同一个mesh，可以单独修改mesh的位置(position)、缩放(scale)、旋转角度(rotation/Quaternion)等属性；也可以单独修改
geometry和material；同一个geometry可以单独修改各个属性和索引。方便管理，且减少对象的局部变化带来的整个drawCommand重新构建次数，以期在更多动态渲染
对象同时改变的情况下（比如结合物理引擎做模拟），仍然保持较高的帧率，当然也牺牲了一定的内存。
* 4、定义[MeshMaterial](https://mikeswei.github.io/CesiumMeshVisualizer/Document/Cesium.MeshMaterial.html)，目的是更方便的引用Threejs圈的那些炫酷特效Shader，减少整合所需的代码量。尤其是Cesium的Appearance从fragmentShader中分离出Material部分的Shader，并且作用域不一样之后，带来好多不便，引入网上那些炫酷特效Shader的时候真是头疼死了，谁做过谁才解其中味~   [MeshMaterial](https://mikeswei.github.io/CesiumMeshVisualizer/Document/Cesium.MeshMaterial.html)区别于Cesium的Material，更像Threejs的Material,但不完全是。。。好像有点四不像~  
* 5、定义[FramebufferTexture](https://mikeswei.github.io/CesiumMeshVisualizer/Document/Cesium.FramebufferTexture.html)，启发于Threejs的RenderTarget，暂且叫帧缓存纹理类吧，反正名字不重要~ MeshVisualizer和MeshMaterial一起支持此类纹
理，就当是普通的一张图片纹理就好了。它的用处就是，把Mesh渲染到帧缓存中，作为纹理参与其他Mesh的渲染。可以参考[VolumeRendering示例](https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/VolumeRendering/index.html)。另外RendererUtils
提供了一个单次执行渲染到纹理的接口[RendererUtils](https://mikeswei.github.io/CesiumMeshVisualizer/Document/Cesium.RendererUtils.html).renderToTexture。在不使用[MeshVisualizer](https://mikeswei.github.io/CesiumMeshVisualizer/Document/Cesium.MeshVisualizer.html)的时候，也就是自己基于drawCommand自定义一个Primitive的时
候也可以用得上。
* 6、整合[CSG.js](https://github.com/evanw/csg.js)，支持使用Cesium.Geometry、THREE.Geometry、THREE.BufferGeometry做交、并、补等运算，并提供将运算结果CSG对象转成Cesium.Geometry、THREE.Geometry的接口。参考[CSG示例](https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/CSG/index.html)。
* 7、提供基于[ammo.js](https://github.com/kripken/ammo.js)物理引擎的示例，[示例](https://mikeswei.github.io/CesiumMeshVisualizer/App/demo/physics/helloworld.html)源码源于Threejs，使用[MeshVisualizer](https://mikeswei.github.io/CesiumMeshVisualizer/Document/Cesium.MeshVisualizer.html)，可以在更少的代码修改情况下完成迁移整合，甚合我意。
    
