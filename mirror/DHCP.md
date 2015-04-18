## Contents

    

1. 개요 
2. 임대(Lease), 갱신(Renewal), 반환(Release) 
    

2.1. 임대(Lease)

2.2. 갱신(Renewal)

2.3. 반환(Release)

3. DHCP 임대 생성(Lease Generation) 
4. DHCP 임대 갱신(Lease Renewal) 
5. 기타 

[[edit](http://rigvedawiki.net/r1/wiki.php/DHCP?action=edit&section=1)]

## 1. 개요 ¶

**D**ynamic **H**ost **C**onfiguration **P**rotocol  
간단하게 해석하자면 동적 호스트 설정 프로토콜이다.

  

[IP](IP.md) [라우터](%EB%9D%BC%EC%9A%B0%ED%84%B0.md)는 인터페이스 및 호스트에 IP 주소를
할당해 줄 수 있다. 예전에는 각 PC마다 고정 IP 설정으로 입력해 주었지만, 너무 번거로운데다가 오타가 날 경우 인터넷이 먹통이
되어버리는 경우(예를들어 IP 주소가 10.10.10.2인데 [오타](%EC%98%A4%ED%83%80.md)가 나서
10.10.1.2라고 입력 된다거나)가 종종 있기 때문에 라우터단에서 자동으로 IP를 할당해 주기 위해 사용한다. 물론 라우터의 기능을 할
수 있는 [서버](%EC%84%9C%EB%B2%84.md)에 서비스를 올릴 수도 있다. 라우터는 단지
[게이트웨이](%EA%B2%8C%EC%9D%B4%ED%8A%B8%EC%9B%A8%EC%9D%B4.md) 역할만 하고, DHCP 서버는
별도로 두는 구성도 가능하다. 이는 아래 참조.

  

[[edit](http://rigvedawiki.net/r1/wiki.php/DHCP?action=edit&section=2)]

## 2. 임대(Lease), 갱신(Renewal), 반환(Release) ¶

DHCP 서버의 임대, 갱신, 반환 과정에 대해 서술되었다.

  

[[edit](http://rigvedawiki.net/r1/wiki.php/DHCP?action=edit&section=3)]

### 2.1. 임대(Lease) ¶

DHCP의 경우 IP를 할당해 주는 것을 **임대(Lease)**라고 한다. 이 임대에는 임대 기간이 있는데, 말 그대로 해당 IP 주소를
사용할 수 있는 기간을 말한다. 즉, **임대 기간이 끝나면 해당 IP 주소는 DHCP 주소 풀로 반환이 된다는 것**이다. 임대 기간은
기본적으로 8일이다. 쉽게 말해 자동차 리스한 다음 재계약 안 해서 도로 차 뺏기는(...) 상황이라고 생각하면 된다. 임대 기간은
기본적으로 8일이며, 장소에 따라서 적당한 값을 찾아서 설정하면 된다. 유동인구가 많은 가게 등지에서 임대 기간을 8일로 두게 된다면
[순식간에 주소 풀이 동날 것이고, 더 이상 IP 주소를 할당할 수 없게되어버린다.](%EB%82%B4%EA%B0%80%20%EA%B3%A0%EC%9E%90%EB%9D%BC%EB%8B%88.md) 이런
유동인구가 많은 지역에서는 임대 기간을 2~3시간 정도로 짧게 설정하는 편이 좋다. <del>아니면 사설 IP 대역을 10.0.0.0/8
대역으로 바꿔버리던지.</del> 가정이나 기업 등에서는 기본값 8일~30일 등으로 길게 잡는다.
[아이피타임](%EC%95%84%EC%9D%B4%ED%94%BC%ED%83%80%EC%9E%84.md) 공유기의 경우 기본적으로 임대
기간이 7200초(2시간)이다.

  

[[edit](http://rigvedawiki.net/r1/wiki.php/DHCP?action=edit&section=4)]

### 2.2. 갱신(Renewal) ¶

임대 기간이 끝나면 IP 주소를 반환하게 된다. 반환에 대한 자세한 정보는 아래 반환에서 확인하자. 만약 임대 기간이 1시간이라면,
1시간마다 IP 주소를 반환하고 다시 임대하면 불필요한 브로드캐스트 트래픽을 발생시키게 된다. 전혀 효율적이지 않기 때문에 DHCP에는 임대
갱신(Lease Renewal)이라는 것이 있다. 쉽게 말해, 자동차 리스한 다음 임대 기간이 끝나기 전에 재계약해서 차 안 뺏기게(...)
하는 것이다. 이 임대 갱신은 두 차례 시도를 하게 되는데, 첫 시도는 임대 기간이 50% 남았을 때 시도된다. 이 때, 갱신에 성공하면
갱신된 시간으로 부터 임대 기간이 다시 차게 된다. 하지만 클라이언트의 전원이 꺼져 있다던가, 아니면 모종의 이유(DHCP 서버가 점검
중이었다거나)로 갱신에 실패하게 된다면, 한번의 기회를 더 준다. 87.5%의 시간이 지났을 때 다시 갱신을 시도한다. 이 때, 갱신에
성공한다면 그 시점으로부터 임대 기간이 다시 차게 된다. 간단하계 예시를 들어보자면, 임대 기간이 10시간이라면, 임대가 생성된 시간으로
부터 50%가 지난 시점인 5시간 뒤에 1차적으로 갱신을 시도하게 되고, 이때 성공한다면 처음 생성된 시간으로 부터 15시간 뒤가 임대가
종료되는 시간이다. 1차 갱신에 실패한다면, 임대가 생성된 시간으로 부터 87.5%가 지난 시점인 8시간 45분 뒤에 2차 갱신을 시도한다.
이때 성공한다면, 임대 생성시간으로부터 18시간 45분 뒤가 임대 종료 시점이 되는 것이다. 만약, 2차 마저 실패했다면, 임대 기간이
끝나는 시점에 주소를 반환하고 임대 생성부터 다시 하게 된다. 즉, 클라이언트가 IP 주소를 계속 사용중이라면, 해당 IP 주소를 계속
사용할 수 있도록 임대 기간을 계속 늘려 주고, 사용하지 않는다면 주소를 반환하여 다른 클라이언트가 사용할 수 있도록 해 주는 것이 바로
임대와 갱신이다.

  

[[edit](http://rigvedawiki.net/r1/wiki.php/DHCP?action=edit&section=5)]

### 2.3. 반환(Release) ¶

임대 기간이 끝났거나, IP 주소를 더 이상 사용하지 않는다면 DHCP 서버에 IP 주소를 반환하게 되는데, 이것을
반환(Release)이라고 한다. DHCP 서버와 클라이언트 모두 임대 기간에 대한 정보를 가지고 있기 때문에, 임대 기간이 끝났는데
클라이언트가 연결되지 않았더라도 해당 주소를 사용 가능하도록 주소 풀로 보낸다. 흔히 인터넷이 안 된다거나, 랜선이랑 다 제대로 꽂혀 있는데
인터넷이 안 된다거나, ipconfig을 쳤는데 IP 주소가 169.254.x.x, 서브넷 마스크가 255.255.0.0으로 나온다면 가장
먼저 해 보라고 하는 것이 명령 프롬프트에 ipconfig /release를 입력하고, ipconfig /renew를 입력하라고 하는
것인데`[1]` 이렇게 한다면 다 멀쩡하다는 전제 하에 IP 주소를 다시 할당받을 수 있도록 유도해 주는 것이다. 반환 과정이 없다면, 고정
IP를 사용하는 것보다 단점이 더 많은 [망한](%EB%A7%9D%ED%96%88%EC%96%B4%EC%9A%94.md) 프로토콜이
되기 때문에 반환 과정은 DHCP에 있어서 임대 생성/갱신보다 중요하다고 볼 수 있겠다.

  

[[edit](http://rigvedawiki.net/r1/wiki.php/DHCP?action=edit&section=6)]

## 3. DHCP 임대 생성(Lease Generation) ¶

DHCP로 임대를 생성할 경우 4가지 과정을 통해서 IP 주소를 할당하게 된다.  
**1\. DHCP Discover.**   

IP 주소가 할당되지 않은 클라이언트는 MAC 주소를 기반으로 IP 주소를 받아오기 위해 로컬 네트워크에 Discover 패킷을
Broadcast 한다. 이 때, Broadcast로 뿌려진 패킷은 해당 네트워크 내의 모든 호스트에게 날아가게 된다. 이 Discover
패킷에는 IP 주소가 필요한 호스트의 MAC 주소가 담겨져 있어서 DHCP 서버가 응답할 때 패킷을 수신할 수 있게 된다. 호스트들이 패킷을
받고 나서, DHCP 서버가 Discover 패킷을 받게 되면 2단계로 넘어간다. 물론 이 때, 다른 호스트들은 어? 내 패킷이 아니네?
하고 패킷을 폐기한다.

**2\. DHCP Offer.**  

DHCP 서버가 Discover 패킷을 받게 되면, DHCP 서버는 Offer 패킷을 Broadcast한다. 이 때, MAC 주소에 아까
Discover 패킷을 보낸 호스트의 MAC 주소를 적어서 보낸다. 마찬가지로 Broadcast이기 때문에 해당 네트워크의 호스트 전체에
패킷이 날아가지만 역시 Discover를 보낸 호스트 이외의 다른 호스트는 패킷을 전부 폐기한다. Discover를 보낸 호스트가 Offer
패킷을 받으면 3단계로 넘어간다.

**3\. DHCP Request.**  

해당 호스트는 Offer를 받았으면 해당 네트워크에는 DHCP 서버가 있다고 판단, Request 패킷을 Broadcast한다.
Request 패킷은 말 그대로 **IP 내놔**. 이 역시 Broadcast이기 때문에 해당 네트워크의 호스트 전체에게 날아가고, DHCP
서버 이외의 다른 호스트는 패킷을 폐기한다. DHCP 서버가 Request 패킷을 받으면 4단계로 진행한다.

**4\. DHCP ACK.**  

DHCP 서버는 Request 패킷을 받으면 자신의 IP 임대 풀에서 할당 가능한 IP 주소를 찾는다. 예를들어 게이트웨이와 DHCP가 모두
192.168.0.1일 경우 192.168.0.1을 제외한 0.2~0.254까지의 임대 풀을 가지고 있다. 이 임대 풀에서 가장 앞에 있는
IP 주소부터 할당하게 되는데, 사용중이 아닌 경우엔 해당 IP 주소를 할당을 하게 된다. 여기서는 192.168.0.2를 할당한다고 가정
하면, "Your (Client) IP Address: 192.168.0.2 (192.168.0.2)" 이런 패킷을 날리게 된다. 역시
Broadcast로 전송되며, 호스트가 이 패킷을 정상적으로 받았다면 IP 주소가 할당된다.  

이 과정을 줄여서 흔히 DORA라고 부른다.

  

이 편리해 보이는 DHCP 서버에도 약간의 문제가 있는데, DHCP 옵션을 설정하지 않을 경우 IP 주소와 서브넷 마스크만 할당하게 된다.
이러면 당연히 통신이 안되는게 당연지사. 그래서 DHCP 옵션에는 기본 게이트웨이 주소, DNS 서버 주소 등이 들어가게 된다.

  

하지만 위의 문제보다 약간 심각한(...) 문제가 하나 더 있다. 바로 고정 IP 주소도 사용하는 네트워크일 경우이다. DHCP 서버는 단지
자신의 임대 가능한 IP 주소 풀에서 안 쓰는 IP를 하나 주는 것이지, 그 IP가 실제 사용중인지는 모른다. 물론 자신이 할당했다면
기억하지만. 즉, 게이트웨이이자 DHCP 서버가 192.168.0.1이고, 192.168.0.2부터 192.168.0.10까지는 고정 IP로
사용중인데 주소 풀이 192.168.0.2부터 시작한다면 DHCP 서버가 있어도 IP 주소가 충돌하기 때문에 클라이언트에 IP가 할당되지
않는다. 이런 경우, 주소 풀을 옮겨 주면 해결이 된다. 또는 DHCP 예약 기능을 이용하여 DHCP를 쓰면서도 고정 IP 효과를 낼 수도
있고.

  

DHCP Relay Agent라는것이 있는데, Broadcast 패킷은 라우터, 즉 게이트웨이를 넘어가지 못한다. 대부분의 가정의 경우 그냥
공유기 아래에 물려 있으니 상관이 없지만, 기업 레벨에서는 DHCP 서버가 라우터 너머에 있는 경우가 종종 있다. 하지만 Broadcast
패킷은 라우터를 넘어가지 못하는데 위에서 봤다시피 IP 할당 과정 4단계가 전부 브로드캐스트로 이루어진다. 이런 경우 라우터 단에서 DHCP
Relay Agent를 설정해서 DHCP 요청이 라우터로 들어오면, 라우터에서 유니캐스트로 변환하여 DHCP서버로 패킷을 보낸다. DHCP
서버에선 역시 유니캐스트로 라우터에게 보내고 라우터는 브로드캐스트로 뿌린다. 이런 식으로 구성하는 경우도 있다.

  

[[edit](http://rigvedawiki.net/r1/wiki.php/DHCP?action=edit&section=7)]

## 4. DHCP 임대 갱신(Lease Renewal) ¶

임대 갱신의 경우 Discover, Offer 과정을 제외한 Request, ACK 과정만을 DHCP 서버와 1:1로 Unicast
통신한다. 이미 IP 주소가 있는데다가, DHCP 서버 주소를 알고 있기 때문에 굳이 Broadcast로 네트워크 상에 쓸데없는 트래픽을
보낼 이유가 없기 때문.  
갱신 과정은 다음과 같다.

  

**1\. DHCP Request**  

DHCP 서버에 갱신을 요청하는 Request 패킷을 Unicast로 보낸다. DHCP 서버가 이 패킷을 받으면, 2단계로 넘어간다.

**2\. DHCP ACK**  

DHCP 서버에서 Request를 요청한 클라이언트에게 ACK 패킷을 Unicast로 보낸다. 이 패킷에는 갱신에 대한 정보가 담겨 있다.  

갱신 과정은 임대 생성에 비해서 간단하다. 그냥 말 그대로 기간만 좀 늘려달라고 부탁하는 것이기 때문.

  

[[edit](http://rigvedawiki.net/r1/wiki.php/DHCP?action=edit&section=8)]

## 5. 기타 ¶

이 DHCP 서버에도 취약점이 있는데, 공격자가 **모든 IP 대역을 DHCP 서버에 반환해버리고 자기가 할당 가능한 주소 풀 전체를 다
먹어버리는 공격 및 자기가 DHCP 서버로 활동해 중간자 공격**이 가능하다. [더이상은](%EC%BD%94%EB%A0%81%ED%83%95.md) [설명할 수 없으므로 더 이상의 자세한 설명은 생략한다](%EB%8D%94%20%EC%9D%B4%EC%83%81%EC%9D%98%20%EC%9E%90%EC%84%B8%ED%95%9C%20%EC%84%A4%EB%AA%85%EC%9D%80%20%EC%83%9D%EB%9E%B5%ED%95%9C%EB%8B%A4.md).

`\----`

  * `[1]` ipconfig /release는 DHCP 서버에 IP 주소를 반환하는 명령어고, ipconfig /renew는 설정된 IP 주소가 없다면 임대 생성을, 이미 IP 주소가 있다면 임대 갱신을 해 주는 명령어이다. renew는 renewal을 줄인 것이다.
