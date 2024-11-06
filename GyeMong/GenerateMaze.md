게임 계몽에서 사용되는 절차적 생성을 통한 미로를 만드는 코드입니다.<br>
미로를 만들때 사용되는 알고리즘은 DFS(Depth First Search)입니다.

<pre><code>
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Tilemaps;

public class GenerateMaze : MonoBehaviour
{
    [SerializeField] Tilemap wallTilemap;
    [SerializeField] Tilemap floorTilemap;
    [SerializeField] Tile wallTile;
    [SerializeField] Tile floorTile;

    private int width = 49;
    private int height = 49;
    private bool[,] gridTile;
    private Stack<Vector2Int> pathStack = new();

    private Vector2Int start; 

    void Start()
    {
        gridTile = new bool[width, height];
        start = new Vector2Int(1, height - 2); // 왼쪽 위(시작지점 or 끝지점)

        InitializeWalls();
        GenerateMap(start);
        GenerateTile(gridTile);
    }

    void GenerateMap(Vector2Int start)
    {
        pathStack.Push(start);
        gridTile[start.x, start.y] = true;

        while (pathStack.Count > 0)
        {
            Vector2Int current = pathStack.Peek();
            List<Vector2Int> neighbors = GetUnvisitedNeighbors(current);

            if (neighbors.Count > 0)
            {
                Vector2Int chosen = neighbors[Random.Range(0, neighbors.Count)];
                pathStack.Push(chosen);

                Vector2Int wallToRemove = (current + chosen) / 2;
                gridTile[wallToRemove.x, wallToRemove.y] = true;
                gridTile[chosen.x, chosen.y] = true;
            }
            else
            {
                pathStack.Pop();
            }
        }

        gridTile[width - 2, 0] = true;
        gridTile[1, height - 1] = true;
    }

    void InitializeWalls()
    {
        for (int i = 0; i < width; i++)
        {
            for (int j = 0; j < height; j++)
            {
                gridTile[i, j] = false;
            }
        }
    }

    List<Vector2Int> GetUnvisitedNeighbors(Vector2Int cell)
    {
        List<Vector2Int> neighbors = new();

        if (cell.x - 2 >= 0 && !gridTile[cell.x - 2, cell.y]) neighbors.Add(new Vector2Int(cell.x - 2, cell.y));
        if (cell.x + 2 < width && !gridTile[cell.x + 2, cell.y]) neighbors.Add(new Vector2Int(cell.x + 2, cell.y));
        if (cell.y - 2 >= 0 && !gridTile[cell.x, cell.y - 2]) neighbors.Add(new Vector2Int(cell.x, cell.y - 2));
        if (cell.y + 2 < height && !gridTile[cell.x, cell.y + 2]) neighbors.Add(new Vector2Int(cell.x, cell.y + 2));

        return neighbors;
    }

    void GenerateTile(bool[,] gridTile)
    {
        wallTilemap.ClearAllTiles();
        floorTilemap.ClearAllTiles();

        for (int i = 0; i < gridTile.GetLength(0); i++)
        {
            for (int j = 0; j < gridTile.GetLength(1); j++)
            {
                Vector3Int tilePosition = new(i - width / 2, j - height / 2, 0);

                if (gridTile[i, j])
                {
                    floorTilemap.SetTile(tilePosition, floorTile);
                }
                else
                {
                    wallTilemap.SetTile(tilePosition, wallTile);
                }
            }
        }
    }
}
</code></pre>

원리는 다음과 같습니다.<br>
1. 코드가 실행되면, 제일먼저 모든 미로를 벽으로 채웁니다.<br>
2. 모든 가장자리는 벽으로 만들기 위해 시작 지점을 (1, height - 2)으로 잡습니다. (끝지점이라 해도 무방)<br>
2.5 GetUnvisitedNeighbors함수는 현재 위치에서 상하좌우로 2칸씩 이동한 칸에서의 bool값(벽,바닥) 상태를 확인하고 벽 상태인 위치만 반환합니다 (Unvisited)<br>
3. 이후 GenerateMap함수가 실행되는데, 
