### 게임 계몽에서 사용되는 절차적 생성을 통한 미로를 만드는 코드.<br>
미로를 만들때 사용되는 알고리즘은 DFS(Depth First Search)입니다.

```cs
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
```


### 원리는 다음과 같습니다.<br>
1. 코드가 실행되면, 제일먼저 **모든 미로를 벽으로 채웁니다**.<br>
2. 모든 가장자리는 벽으로 만들기 위해 시작 지점을 (1, height - 2)으로 잡습니다. (끝지점이라 해도 무방)<br>
2.5 GetUnvisitedNeighbors함수는 현재 위치에서 상하좌우로 2칸씩 이동한 칸에서의 bool값(벽,바닥) 상태를 확인하고 벽 상태인 위치만 반환합니다. **즉 Unvisited인 이웃을 반환합니다**.<br>
3. 이후 GenerateMap함수 실행. 이 함수는 시작점을 변수로 받아서 **시작점부터 Unvisited인 이웃중 랜덤으로 하나를 선택해 벽을 뚫는** 함수입니다. 여기에 스택을 사용한 백트래킹 기법을 이용합니다. 이를통해 미로내의 어떠한 두 점을 가져와도 두 점을 이어주는 경로는 유일하게 하나로 보장됩니다.<br>
4. 최종적으로 결정된 2차원 bool배열에서 false를 벽으로 true를 바닥으로 타일맵을 이용해서 배치합니다.<br>
4.5 벽 타일맵은 collider(Tilemap Collider, Composite Collider)를 이용해 벽으로 설정합니다.<br>

### DFS가 어떻게 사용되었나?<br>
![image](https://github.com/user-attachments/assets/4833ad42-020c-49e5-826e-5471556cdb97)<br>
그림과 같이 9개의 점으로 구성된 미로가 있다고 가정해보자.

