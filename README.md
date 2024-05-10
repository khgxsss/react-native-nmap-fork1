react-native naver map [![npm version](https://badge.fury.io/js/react-native-nmap-fork1.svg)](https://badge.fury.io/js/react-native-nmap-fork1)
-----

네이버맵의 리액트 네이티브 브릿지입니다. 

유지보수가 안되어 개인적으로 수정해서 쓰고 있습니다. fork by doodoo13

원본 : https://github.com/QuadFlask/react-native-naver-map

![](https://raw.githubusercontent.com/QuadFlask/react-native-naver-map/master/example/screenshot/screenshot.png)


## 설치

```
npm install react-native-nmap-fork1 --save;
```

- **React Native 0.60+**

```bash
$ cd ios/ && pod install
```

- **React Native <= 0.59**

```bash
$ react-native link react-native-nmap-fork1
$ cd ios/ && pod install
```

> ios의 경우 `git-lfs` 설치가 필요합니다. [참고](https://github.com/navermaps/ios-map-sdk#%EB%8C%80%EC%9A%A9%EB%9F%89-%ED%8C%8C%EC%9D%BC%EC%9D%84-%EB%B0%9B%EA%B8%B0-%EC%9C%84%ED%95%B4-git-lfs-%EC%84%A4%EC%B9%98%EA%B0%80-%ED%95%84%EC%9A%94%ED%95%A9%EB%8B%88%EB%8B%A4)


### 안드로이드 추가 설정

[네이버 맵 안드로이드 SDK 문서](https://navermaps.github.io/android-map-sdk/guide-ko/1.html)를 따라 API키와 레포지터리 경로를 추가합니다

`/android/build.gradle` 파일에 아래와 같이 레포지터리를 추가합니다

> ⚠️ Bintray 지원 중단에 의해 jfrog로 수정되었습니다

```
allprojects {
    repositories {
        google()
        mavenCentral()
        // 네이버 지도 저장소
        maven {
            url 'https://naver.jfrog.io/artifactory/maven/'
        }
    }
}
```

`/android/app/src/main/AndroidManifest.xml`에 아래와 같이 추가하고 발급받은 클라이언트 아이디로 바꿔줍니다.
```xml
<manifest>
    <application>
        <meta-data
            android:name="com.naver.maps.map.CLIENT_ID"
            android:value="YOUR_CLIENT_ID_HERE" />
    </application>
</manifest>
```

### IOS 추가 설정

[네이버 맵 IOS SDK 문서](https://navermaps.github.io/ios-map-sdk/guide-ko/1.html)를 따라 API키와 레포지터리 경로를 추가합니다.

`info.plist`에 아래와 같이 발급받은 클라이언트 아이디를 추가해줍니다.

![image](https://user-images.githubusercontent.com/49827449/66392740-b2fd5f00-ea0b-11e9-8c38-23e604b1009d.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
...
    <key>NMFClientId</key>
    <string>YOUR_CLIENT_ID_HERE</string>
...
<dict>
<plist>
```

## 예제

> code block

```tsx
import NaverMapView, { Coord, Marker, NaverMapViewInstance, Polyline } from 'react-native-nmap-fork1';

return (
        <NaverMapView 
            showsMyLocationButton={false}
            compass={true}
            scaleBar={true}
            nightMode={false}
            zoomControl={true}
            // logoMargin={{left: 50}} 네이버 맵 정책으로 반드시 보여야 함
            mapType={mapType}
            style={{ height:"100%" }}
            center={locationSaved ? {latitude: locationSaved.latitude, longitude:locationSaved.longitude, zoom: locationSaved.mapZoomLevel}:{latitude: 37.35882350130591, longitude: 127.10469231924353, zoom: 13}}
            onCameraChange={handleOnCameraChange}
            onTouch={handleOnTouch}
            ref={mapView}
        >
            {userMarker}
            {deviceMarkers}
            {seeDistanceLines && distanceLines}
        </NaverMapView>
)
}
```


## 컴포넌트

타입스크립트 타입 정의가 포함되어 있어 타입스크립트 사용을 추천합니다.

### 기본 타입

```ts
export interface Coord {
    latitude: number;
    longitude: number;
}
export interface Region extends Coord {
    latitudeDelta: number;
    longitudeDelta: number;
}
export interface Rect {
    left?: number;
    top?: number;
    right?: number;
    bottom?: number;
}
```

### `NaverMapView`
```ts
interface NaverMapViewProps {
    center?: Coord & {
        zoom?: number;
        tilt?: number;
        bearing?: number;
    };
    tilt?: number;
    bearing?: number;
    mapPadding?: Rect;
    logoMargin?: Rect;
    logoGravity?: Gravity;
    onInitialized?: Function;
    onCameraChange?: (event: {
        latitude: number;
        longitude: number;
        zoom: number;
        contentRegion: [Coord, Coord, Coord, Coord, Coord]; // https://navermaps.github.io/android-map-sdk/reference/com/naver/maps/map/NaverMap.html#getContentRegion()
        coveringRegion: [Coord, Coord, Coord, Coord, Coord];
    }) => void;
    onMapClick?: (event: {
        x: number;
        y: number;
        latitude: number;
        longitude: number;
    }) => void;
    onTouch?: () => void;
    showsMyLocationButton?: boolean;
    compass?: boolean;
    scaleBar?: boolean;
    zoomControl?: boolean;
    mapType?: MapType;
    minZoomLevel?: number;
    maxZoomLevel?: number;
    buildingHeight?: number;
    nightMode?: boolean;
    scrollGesturesEnabled?: boolean;
    zoomGesturesEnabled?: boolean;
    tiltGesturesEnabled?: boolean;
    rotateGesturesEnabled?: boolean;
    stopGesturesEnabled?: boolean;
    useTextureView?: boolean; // android only
}
/// component method
animateToCoordinate: (coord: Coord) => void;
animateToTwoCoordinates: (c1: Coord, c2: Coord) => void;
animateToCoordinates: (coords: Coord[], bounds?: {
    top: number;
    bottom: number;
    left: number;
    right: number;
}) => void;
animateToRegion: (region: Region) => void;
setLocationTrackingMode: (mode: number) => void;
setLayerGroupEnabled: (group: LayerGroup, enabled: boolean) => void;
showsMyLocationButton: (show: boolean) => void;
handleOnCameraChange: (event: React.SyntheticEvent<{}, {
    latitude: number;
    longitude: number;
    zoom: number;
}>) => void;
handleOnMapClick: (event: React.SyntheticEvent<{}, {
    x: number;
    y: number;
    latitude: number;
    longitude: number;
}>) => void;
```

### `Marker`
```ts
interface MarkerProps {
    coordinate: Coord
    anchor?: { x: number, y: number }
    pinColor?: string
    alpha?: number
    rotation?: number
    flat?: boolean
    image?: ImageSourcePropType
    onClick?: () => void
    width?: number
    height?: number
    angle?: number
    hidden?: boolean
    zIndex?: number
    iconPerspectiveEnabled?: boolean
    isHideCollidedSymbols?: boolean
    isHideCollidedMarkers?: boolean
    isHideCollidedCaptions?: boolean;
    isForceShowIcon?: boolean;
    caption?: {
        text?: string;
        align?: Align;
        textSize?: number;
        color?: string;
        haloColor?: string;
        offset?: number;
        requestedWidth?: number;
        minZoom?: number;
        maxZoom?: number;
    };
    subCaption?: {
        text?: string;
        textSize?: number;
        color?: number;
        haloColor?: number;
        requestedWidth?: number;
        minZoom?: number;
        maxZoom?: number;
    };
}
```
> 안드로이드 플랫폼에서 마커내 커스텀 뷰를 지원합니다. `0.0.57`
> ```js
> <Marker coordinate={P5} width={96} height={96}>
>     <View style={{backgroundColor: 'rgba(255,0,0,0.2)', borderRadius: 80}}>
>         <View style={{backgroundColor: 'rgba(0,0,255,0.3)', borderWidth: 2, borderColor: 'black', flexDirection: 'row'}}>
>             <Image source={require("./marker.png")} style={{
>                 width: 32, height: 32,
>                 backgroundColor: 'rgba(0,0,0,0.2)', resizeMode: 'stretch',
>                 borderWidth: 2, borderColor: 'black'}} fadeDuration={0}/>
>             <Text>Image</Text>
>         </View>
>         <ImageBackground source={require("./marker.png")} style={{width: 64, height: 64}}>
>             <Text>image background</Text>
>         </ImageBackground>
>     </View>
> </Marker> 
> ```

### `Polyline`
```ts
interface PolylineProps {
    coordinates: Coord[]
    strokeWidth?: number
    strokeColor?: string
    onClick?: () => void
}
```

### `Path`
```ts
interface PathProps {
    coordinates: Coord[]
    width?: number
    color?: string
    outlineWidth?: number
    passedColor?: string
    outlineColor?: string
    passedOutlineColor?: string
    pattern?: ImageSourcePropType
    patternInterval?: number
    onClick?: () => void
}
```

### `Circle`
```ts
export interface CircleProps {
    coordinate: Coord[]
    radius?: number;
    color?: string;
    outlineWidth?: number;
    outlineColor?: string;
    zIndex?: number;
    onClick?: () => void
}
```

### `Polygon`
```ts
export interface PolygonProps {
    coordinate: Coord[]
    outlineWidth?: number;
    outlineColor?: string
    color?: string;
    holes?: Coord[][];
    onClick?: () => void
}
```

### `LayerGroup`
```ts
export declare enum LayerGroup {
    LAYER_GROUP_BUILDING = "building",
    LAYER_GROUP_TRANSIT = "transit",
    LAYER_GROUP_BICYCLE = "bike",
    LAYER_GROUP_TRAFFIC = "ctt",
    LAYER_GROUP_CADASTRAL = "landparcel",
    LAYER_GROUP_MOUNTAIN = "mountain"
}
```

### `NaverMapViewInstance`
```ts
export interface NaverMapViewInstance extends NaverMapView {
    animateToCoordinate: (coord: Coord) => void;
    animateToTwoCoordinates: (c1: Coord, c2: Coord) => void;
    animateToCoordinates: (coords: Coord[], bounds?: { top: number, bottom: number, left: number, right: number, }) => void;
    animateToRegion: (region: Region) => void;
    setLocationTrackingMode: (mode: number) => void;
    setLayerGroupEnabled: (group: LayerGroup, enabled: boolean) => void;
    showsMyLocationButton: (show: boolean) => void;
    nodeHandle?: null | number;

    // props
    props: NaverMapViewProps;
}

예제

const mapView = useRef<NaverMapViewInstance>(null);
position => {
            const { latitude, longitude } = position.coords;
            mapView.current?.animateToCoordinate({ latitude, longitude });
        },

...
<NaverMapView 
    ref={mapView}
>
    {...}
</NaverMapView>
```

## 참고

- *react-navigation*의 스택 스크린 사용시 안드로이드에서 맵뷰가 겹쳐 보이는 현상이 있을 경우 `useTextureView` 옵션을 추가해 주세요. [#27](https://github.com/QuadFlask/react-native-naver-map/issues/27)

- 안드로이드에서 `ScrollView` 내부에 추가할 경우 `scrollGesturesEnabled`를 이용해 맵 스크롤을 제어할 수 있습니다. [#62](https://github.com/QuadFlask/react-native-naver-map/issues/62)

- 마커 클러스터링에 필요한 지도 컨텐츠 영역은 `onCameraChange` 이벤트를 통해 얻을 수 있습니다. [#64](https://github.com/QuadFlask/react-native-naver-map/issues/64)