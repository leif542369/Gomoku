# Gomoku
1v1 Online Gomoku
联机1v1 网页版五子棋
firebaseapp.com须要注册一个帐号然后配置数据库
在线五子棋游戏使用 Firebase 实现，主要依赖 Firebase Realtime Database（实时数据库）实现实时数据同步，并结合 Firebase Authentication（身份验证）和 Firebase Hosting（静态托管）等功能。以下是关键步骤和实现思路：

1. 初始化 Firebase 项目
创建 Firebase 项目

访问 Firebase 控制台，创建新项目。

启用 Realtime Database 和 Authentication（如需要用户登录）。

配置前端应用

在项目中注册 Web 应用，获取 firebaseConfig。

在代码中初始化 Firebase：
2. 设计数据库结构
实时数据库的 JSON 结构示例：
{
  "games": {
    "game1": {
      "player1": "user123",
      "player2": "user456",
      "currentPlayer": "user123",  // 当前轮到的玩家
      "board": [
        [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
        // ...15x15 的棋盘，0=空，1=玩家1，2=玩家2
      ],
      "winner": null,
      "status": "waiting"  // "waiting"、"playing"、"finished"
    }
  },
  "waitingPlayers": []  // 等待匹配的玩家队列
}

6. 设置安全规则
在 Firebase 控制台的 Realtime Database → 规则 中，限制用户仅能修改自己相关的数据：

{
  "rules": {
    "games": {
      "$gameId": {
        // 仅允许玩家修改自己的棋盘位置
        "board": {
          "$row": {
            "$col": {
              ".write": "auth != null && 
                (data.parent().parent().child('player1').val() === auth.uid ||
                 data.parent().parent().child('player2').val() === auth.uid)"
            }
          }
        }
      }
    },
    "waitingPlayers": {
      ".write": "auth != null"
    }
  }
}
