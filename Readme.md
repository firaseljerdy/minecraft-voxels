# Minecraft Voxel Engine in Unity

![Voxel Terrain]([path_to_screenshot.png](https://i.sstatic.net/blbhj.png))

## Introduction

This project is a basic recreation of Minecraft using Unity's powerful engine and voxel technology. It demonstrates how to generate a voxel-based world by creating blocks and chunks, merging meshes for performance optimization, and applying textures through UV mapping. The aim is to provide a foundational understanding of how voxel engines work within Unity, leveraging Unity's Mesh API and, optionally, the Jobs system and Burst compiler for enhanced performance.

## Features

- **Voxel Terrain Generation**: Create a 3D world composed of blocks.
- **Mesh Merging**: Combine multiple block meshes into a single mesh for improved rendering performance.
- **UV Mapping**: Apply textures to block faces using UV coordinates.
- **Custom Block Types**: Implement various block types like grass, dirt, water, stone, and sand.
- **Expandable**: Easily add new block types or expand the world dimensions.

## Getting Started

### Prerequisites

- **Unity Version**: 2019.3 or later (recommended for Mesh API compatibility)
- **Programming Knowledge**: Basic understanding of C# and Unity scripting

### Installation

1. **Clone the Repository**

   ```bash
   git clone https://github.com/yourusername/minecraft-voxel-engine-unity.git
   ```

2. **Open in Unity**

   - Open Unity Hub.
   - Click on **"Add"** and select the cloned project folder.
   - Open the project.

### Running the Project

- Open the **MainScene** located in the `Scenes` folder.
- Press the **Play** button to run the scene and see the voxel terrain.

## Project Structure

```
Assets/
├── Scripts/
│   ├── MeshUtils.cs
│   ├── Block.cs
│   ├── Quad.cs
│   └── Chunk.cs
├── Materials/
│   └── Atlas.mat
├── Textures/
│   └── TextureAtlas.png
├── Scenes/
│   └── MainScene.unity
└── ...
```

## Code Overview

### MeshUtils.cs

This static class contains utility functions and data structures for mesh manipulation and block definitions.

#### Enums

- **BlockType**: Defines different types of blocks.
  ```csharp
  public enum BlockType {
      GRASSTOP, GRASSSIDE, DIRT, WATER, STONE, SAND
  };
  ```
- **BlockSide**: Defines the sides of a block.
  ```csharp
  public enum BlockSide { BOTTOM, TOP, LEFT, RIGHT, FRONT, BACK };
  ```

#### UV Mapping

- **blockUVs**: A 2D array holding UV coordinates for each block type.

  The `blockUVs` array maps each block type to its corresponding UV coordinates in the texture atlas.

  ```csharp
  public static Vector2[,] blockUVs = {
      /*GRASSTOP*/ { new Vector2(0.125f, 0.375f), new Vector2(0.1875f, 0.375f),
                     new Vector2(0.125f, 0.4375f), new Vector2(0.1875f, 0.4375f) },
      /*GRASSSIDE*/ { new Vector2(0.1875f, 0.9375f), new Vector2(0.25f, 0.9375f),
                      new Vector2(0.1875f, 1.0f), new Vector2(0.25f, 1.0f) },
      /*DIRT*/ { new Vector2(0.125f, 0.9375f), new Vector2(0.1875f, 0.9375f),
                 new Vector2(0.125f, 1.0f), new Vector2(0.1875f, 1.0f) },
      /*WATER*/ { new Vector2(0.875f, 0.125f), new Vector2(0.9375f, 0.125f),
                  new Vector2(0.875f, 0.1875f), new Vector2(0.9375f, 0.1875f) },
      /*STONE*/ { new Vector2(0, 0.875f), new Vector2(0.0625f, 0.875f),
                  new Vector2(0, 0.9375f), new Vector2(0.0625f, 0.9375f) },
      /*SAND*/ { new Vector2(0.125f, 0.875f), new Vector2(0.1875f, 0.875f),
                 new Vector2(0.125f, 0.9375f), new Vector2(0.1875f, 0.9375f) }
  };
  ```

#### Methods

- **MergeMeshes(Mesh[] meshes)**: Merges multiple meshes into a single mesh to optimize rendering.

  ```csharp
  public static Mesh MergeMeshes(Mesh[] meshes) {
      // Implementation for merging meshes
  }
  ```

- **ExtractArrays(Dictionary<VertexData, int> list, Mesh mesh)**: Extracts vertex data from a dictionary and applies it to a mesh.

  ```csharp
  public static void ExtractArrays(Dictionary<VertexData, int> list, Mesh mesh) {
      // Implementation for extracting vertex data
  }
  ```

### Block.cs

Represents a single block in the world.

- **Constructor**: Creates quads for each side of the block and merges them into a single mesh.

  ```csharp
  public Block(Vector3 offset, MeshUtils.BlockType type) {
      // Create quads for each side
      // Merge them into one mesh
  }
  ```

### Quad.cs

Represents a single face (quad) of a block.

- **Constructor**: Generates vertices, normals, UVs, and triangles for the quad based on the side and block type.

  ```csharp
  public Quad(MeshUtils.BlockSide side, Vector3 offset, MeshUtils.BlockType bType) {
      // Generate mesh data for the quad
  }
  ```

### Chunk.cs

Represents a chunk of blocks (a section of the world).

- **Variables**:

  ```csharp
  public int width = 2;
  public int height = 2;
  public int depth = 2;
  public Block[,,] blocks;
  ```

- **Start Method**: Initializes the chunk by creating blocks and merging their meshes.

  ```csharp
  void Start() {
      // Create blocks and merge meshes
  }
  ```

- **ProcessMeshDataJob**: (Optional) A struct that implements `IJobParallelFor` to process mesh data in parallel using Unity's Jobs system and Burst compiler.

  ```csharp
  [BurstCompile]
  struct ProcessMeshDataJob : IJobParallelFor {
      // Job implementation for processing mesh data
  }
  ```

## Detailed Explanation

### Mesh Merging for Performance

Rendering a large number of individual meshes (one per block) is inefficient. Mesh merging combines these meshes into a single mesh, reducing draw calls and improving performance.

- **MeshUtils.MergeMeshes()**: Iterates through each block's mesh, collects unique vertices, normals, and UVs, and rebuilds a single mesh.

### UV Mapping

UV mapping is used to apply textures to the blocks correctly.

- **Texture Atlas**: A single texture image that contains all the textures for different block types.
- **UV Coordinates**: Specify which part of the texture atlas to use for each block face.

### Block Construction

Each block is made up of six quads, representing its six faces.

- **Quad Creation**: For each side of the block, a `Quad` is created with appropriate vertices, normals, and UVs.
- **Offset**: Blocks are positioned in the world using an offset vector.

### Chunk Generation

Chunks are collections of blocks.

- **Dimensions**: Controlled by the `width`, `height`, and `depth` variables.
- **Block Storage**: Blocks are stored in a 3D array `blocks[,,]`.
- **Mesh Combination**: The meshes of all blocks within a chunk are combined for efficient rendering.

### Optional Performance Optimization

Using Unity's Jobs system and Burst compiler can significantly improve performance, especially for larger worlds.

- **ProcessMeshDataJob**: Processes mesh data in parallel.
- **Burst Compilation**: Optimizes the job for better performance.

## Customization

### Adding New Block Types

1. **Update BlockType Enum**:

   ```csharp
   public enum BlockType {
       GRASSTOP, GRASSSIDE, DIRT, WATER, STONE, SAND, NEW_BLOCK_TYPE
   };
   ```

2. **Add UV Coordinates**: Update the `blockUVs` array with UV coordinates for the new block type.

   ```csharp
   /*NEW_BLOCK_TYPE*/ { new Vector2(u1, v1), new Vector2(u2, v2),
                        new Vector2(u3, v3), new Vector2(u4, v4) },
   ```

3. **Update Texture Atlas**: Add the new texture to your texture atlas image.

### Changing Chunk Size

Adjust the `width`, `height`, and `depth` variables in **Chunk.cs** to change the dimensions of the world.

```csharp
public int width = 10;
public int height = 10;
public int depth = 10;
```

### Implementing Different Block Placement

Modify the `Chunk.Start()` method to place different types of blocks based on your own logic, such as perlin noise for terrain generation.

```csharp
blocks[x, y, z] = new Block(new Vector3(x, y, z), MeshUtils.BlockType.STONE);
```

## Optimization Tips

- **Cull Hidden Faces**: Do not create quads for block faces that are not visible (e.g., faces between two solid blocks).
- **Mesh Simplification**: Implement greedy meshing algorithms to reduce the number of faces.
- **Level of Detail (LOD)**: Implement LOD systems to reduce detail at a distance.

## Known Issues

- **Performance on Large Worlds**: Generating very large chunks without optimization may lead to performance issues.
- **Texture Bleeding**: Ensure textures in the atlas have proper padding to prevent bleeding.

## Future Improvements

- **Infinite Terrain Generation**: Implement procedural generation for an endless world.
- **Player Interaction**: Enable block breaking and placing.
- **Advanced Rendering**: Add lighting and shading effects.
- **Physics Integration**: Incorporate Unity's physics engine for realistic interactions.

## Resources

- **Unity Mesh API Documentation**: [Unity Mesh](https://docs.unity3d.com/ScriptReference/Mesh.html)
- **Unity Jobs System**: [Unity Jobs](https://docs.unity3d.com/Manual/JobSystem.html)
- **Burst Compiler**: [Unity Burst](https://docs.unity3d.com/Manual/BurstCompiler.html)
- **Voxel Tutorials**: Search for Unity voxel tutorials for more in-depth explanations and advanced techniques.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for any bugs or feature requests.

## Acknowledgments

- **Minecraft**: Inspiration for voxel-based world generation.
- **Unity Community**: For tutorials and support on voxel engines.

## Contact

For any questions or feedback, please contact [your.email@example.com](mailto:firas.aljerdy@gmail.com).

---

*This README provides an overview and documentation for a Unity project that recreates Minecraft-like voxel terrain. It explains the code structure, functionality, and how to get started with the project.*
