# LINE DETECT 

화면에 보이는 라인을 따라서 추적하는 프로그램

### 코드 설명       

#### 화면 처리
```
Mat processFrame(Mat frame) {
    Mat gray, thred, cutthred;
    cvtColor(frame, gray, COLOR_BGR2GRAY); // 그레이스케일 변환
    gray += 100 - mean(gray)[0]; // 영상 밝기 조정
    threshold(gray, thred, 0, 255, THRESH_BINARY | THRESH_OTSU);// 이진화
    thred(Rect(0, 270, 640, 90)).copyTo(cutthred); // 하단 1/4 크기만 추출
    return cutthred;
}
```
1. 입력 영상 그레이스케일로 변환 / 2. 영상 평균밝기 100으로 설정 / 3. 이진화 / 4. 하단 라인부분 편집
   
#### 목표설정, error값
```
void get_target(int labeling, Mat stats, Point& center, Mat centroids, double& closest, int& target, int& error, Mat cutthred){
    closest = DBL_MAX;
    target = 0;
    for (int i = 1; i < labeling; ++i) {
            if (stats.at<int>(i,4) < 50) continue; // 작은 영역 제외

            double dx = center.x - centroids.at<double>(i, 0);
            double dy = center.y - centroids.at<double>(i, 1);
            double distance = (pow(dx, 2) + pow(dy, 2)); // 거리 계산

            if (distance < closest && distance < 5625) { // 75*75 = 5625
                closest = distance;
                target = i;
            }
        }

        // 새로운 중심점 업데이트
        if (target > 0) {
            center.x = centroids.at<double>(target, 0);
            center.y = centroids.at<double>(target, 1);
            error = (cutthred.cols/2) - centroids.at<double>(target, 0);
        }
}
```
1. 가장 가까운 거리값과 타겟 번호 초기화
2. 감지된 객체에서 크기 50미만 제외
3. 이전 중심값에서 현재 측정된 객체의 중심값 거리 계산
4. 가장 가까운 거리, 거리의 값이 75픽셀이하 조건을 충족하면 타겟으로 설정
5. 타겟이 업데이트 되었다면 중심값도 업데이트 해준 뒤, 에러 값 계산
       
#### 목표 화면에 표시
```
void draw_target(int labeling, Mat stats, Mat& cutthred, int target, Point center, Mat centroids){
    for (int i = 1; i < labeling; ++i) {
            int* p = stats.ptr<int>(i);
            if (stats.at<int>(i,4) < 50) continue;
            rectangle(cutthred, Rect(p[0], p[1], p[2], p[3]), Scalar(255, 0, 0), 2);
            if(i == target){
                rectangle(cutthred, Rect(p[0], p[1], p[2], p[3]), Scalar(0, 0, 255), 2);
                circle(cutthred, center, 5, Scalar(0,0,255), -1);
            }
            else if( target == 0 ){
                circle(cutthred, center, 5, Scalar(0,0,255), -1);
                //cout << "line mia" << endl;
            }
        }
}
```
1. 감지된 객체들에 대해서 파란색 사각형
2. 타겟 번호와 일치하는 객체는 빨간색, 중심점 표시
3. 추적중인 객체가 없다면 이전 중심값을 화면에 표시
