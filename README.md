# Sciurus17-jazzy
sciurus17 jazzyをdockerで動かすためのリポジトリ

humble版: https://github.com/ry0hei-kobayashi/sciurus17-humble

本家: https://github.com/rt-net/sciurus17_ros (jazzyブランチ)

## humble版との違い
- ベースイメージが `osrf/ros:jazzy-desktop` (Ubuntu 24.04)
- シミュレータが Gazebo Classic から Gazebo Harmonic (`gz sim`) に変更
- Gazeboのlaunchファイル名が変更
  - `sciurus17_with_table.launch.py` → `sciurus17_gazebo.launch.py`
  - `use_sim_time` はlaunch内で自動設定されるため引数不要
- ArUcoマーカ付きBox / 青色Box付きのworldが追加
  - `sciurus17_with_aruco_cube.launch.py`
  - `sciurus17_with_blue_cube.launch.py`

## 環境構築
```
git clone https://github.com/ry0hei-kobayashi/sciurus17-jazzy.git
cd sciurus17-jazzy/env_docker
docker compose build
```

## docker起動
```
xhost +local:docker
docker compose up
```

docker立ち上げ後に以下のコマンドでコンテナに入る
```
docker exec -it sciurus-jazzy bash
```

## 実機で開発する

### udev_rulesを作成する(初回設定時のみ)
```
ros2 run sciurus17_tools create_udev_rules
```
PCを再起動 or udev_rulesを再読み込み

### 動作確認
sciurus17のbringupを叩く

```
ros2 launch sciurus17_examples demo.launch.py
```

sciurus17_examplesのサンプルを起動する
```
ros2 launch sciurus17_examples example.launch.py example:='pick_and_place_right_arm_waist'
```

## simulatorで開発する

Gazebo (Harmonic) を起動する
```
ros2 launch sciurus17_gazebo sciurus17_gazebo.launch.py
```
testプログラムを実行する
```
ros2 launch sciurus17_examples example.launch.py example:='pick_and_place_right_arm_waist' use_sim_time:='true'
```

カメラのシミュレーションを無効化する場合
```
ros2 launch sciurus17_gazebo sciurus17_gazebo.launch.py use_head_camera:=false use_chest_camera:=false
```

### NVIDIA GPUを使う
Gazebo Harmonicの描画が重い場合は `nvidia-container-toolkit` をインストールし、
`env_docker/compose.yml` の `NVIDIA_*` 環境変数と `deploy:` ブロックのコメントを外す

## ros2 packageを追加する
additional_rospkgsフォルダ以下に追加のros2 packageを配置する

DockerfileでCOPY, compose.ymlでvolumeアタッチされるようになっているのでDocker内外で双方向に編集可能
