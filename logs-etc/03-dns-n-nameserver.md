# 2020/09/03

## DNS와 Nameserver

### Context

기존에 AWS Route53에 등록한 도메인을 그대로 사용하면서, 연결된 IP 주소는 GCP(Google Cloud Platform)의 VM인스턴스로 연결하려 하였다.

GCP의 VM 인스턴스는 외부 고정 IP로 설정하였다. 하지만, IP 주소로는 접속이 되지만 도메인으로는 접속되지 않는 현상이 발생했다.

레코드(record) 세트도 모두 잘 설정되어 있었다.

### TIL

문제는 등록된 도메인의 네임서버(nameserver)가 잘못되어 있었다.

Route53에서 네임서버를 수정하였지만, 계속 반영이 안 되었다가 약 3\~4시간이 지나고는 잘 되는 것을 확인하였다.

업데이트에 최소 2시간부터 48시간까지도 걸린다고하니, 조급하게 확인하지 않도록 하자.
