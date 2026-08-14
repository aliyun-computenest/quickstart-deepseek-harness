<h1> Rapid deployment of the LobeChat computing nest </h1>

<blockquote>
    <p><strong> Disclaimer:</strong> This service is provided by a third party. We try our best to ensure its security,
        accuracy and reliability, but we cannot guarantee that it is completely free from failure, interruption, error
        or attack. Therefore, the company hereby declares that it makes no representations, warranties or commitments
        regarding the content, accuracy, completeness, reliability, suitability and timeliness of the Service and is not
        liable for any direct or indirect loss or damage arising from your use of the Service; for third-party websites,
        applications, products and services that you access through the Service, do not assume any responsibility for
        its content, accuracy, completeness, reliability, applicability and timeliness, and you shall bear the risks and
        responsibilities of the consequences of use; for any loss or damage arising from your use of this service,
        including but not limited to direct loss, indirect loss, loss of profits, loss of goodwill, loss of data or
        other economic losses, even if we have been advised in advance of the possibility of such loss or damage; we
        reserve the right to amend this statement from time to time, so please check this statement regularly before
        using the Service. If you have any questions or concerns about this Statement or the Service, please contact us.
    </p>
</blockquote>

<h2> Overview </h2>

<p>LobeChat is a modern open source ChatGPT/LLMs chat application and development framework <br/>
    It supports speech synthesis, multi-modal, and extensible (<a
            href="https://lobehub.com/zh/blog/openai-function-call">function call</a>) plug-in system. You can use
    LobeChat to have your own ChatGPT/Gemini/Claude/Ollama application for free with one click </p>

<h2> Prerequisites </h2>

<p><font style="color:black;"> To deploy a Dify Community Edition service instance, you need to access and
    create some Alibaba Cloud resources. Therefore, your account must contain permissions for the following
    resources. </font><font style="color:black;"> </font><strong><font style="color:black;">
    Description </font></strong><font style="color:rgb(51, 51>: 51);">: this permission is required only when your account is a RAM account. </font></p>

<table>
<thead>
<tr>
    <th><font style="color:black;"> Permission policy name </font></th>
    <th><font style="color:black;"> Remarks </font></th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td><font style="color:black;">AliyunECSFullAccess</font></td>
        <td><font style="color:black;"> Permissions to manage ECS </font></td>
    </tr>
    <tr>
        <td><font style="color:black;">AliyunVPCFullAccess</font></td>
        <td><font style="color:black;"> Permissions to manage a VPC </font></td>
    </tr>
    <tr>
        <td><font style="color:black;">AliyunROSFullAccess</font></td>
        <td><font style="color:black;"> Manage permissions for the Resource Orchestration Service
            (ROS) </font></td>
    </tr>
    <tr>
        <td><font style="color:black;">AliyunComputeNestUserFullAccess</font></td>
        <td><font style="color:black;"> Manage user-side permissions for the compute nest service
            (ComputeNest) </font></td>
    </tr>
    </tbody>
    </table>

<h2> Billing instructions </h2>

<p><font style="color:black;"> The cost of LobeChat community edition deployment in computing nest mainly
    involves:</font></p>

<ul>
    <li><font style="color:black;"> selected vCPU and memory specifications </font></li>
    <li><font style="color:black;"> System disk type and capacity </font></li>
    <li><font style="color:black;"> Internet bandwidth </font></li>
</ul>

<h2> Deployment Architecture </h2>

<p><img src="./img-en/deploy.png" alt=""/></p>

<h2> Parameter description </h2>

<table>
    <thead>
    <tr>
        <th><font style="color:black;"> parameter group </font></th>
        <th><font style="color:black;"> parameter items </font></th>
        <th><font style="color:black;"> Description </font></th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td><font style="color:black;"> Service instance </font></td>
        <td><font style="color:black;"> Service instance name </font></td>
        <td><font style="color:black;"> No more than 64 characters in length, must start with an English
            letter, and can contain numbers, English letters, dashes (-), and underscores (_)</font></td>
    </tr>
    <tr>
        <td></td>
        <td><font style="color:black;"> Region </font></td>
        <td><font style="color:black;"> Region where the service instance is deployed </font></td>
    </tr>
    <tr>
        <td></td>
        <td><font style="color:black;"> Payment type </font></td>
        <td><font style="color:black;"> Resource billing type: Pay-As-You-Go and Subscription </font></td>
    </tr>
    <tr>
        <td><font style="color:black;">ECS instance configuration </font></td>
        <td><font style="color:black;"> instance type </font></td>
        <td><font style="color:black;"> Instance specifications available in the Availability Zone </font>
        </td>
    </tr>
    <tr>
        <td></td>
        <td><font style="color:black;"> Instance password </font></td>
        <td><font style="color:black;"> is 8-30 in length and must contain three items (uppercase letters,
            lowercase letters, numbers, ()'~! @#$%^&*-+=<{}[]:;'<>,. special symbols in?/)</font></td>
    </tr>
    <tr>
        <td><font style="color:black;"> Network Configuration </font></td>
        <td><font style="color:black;"> Availability Zone </font></td>
        <td><font style="color:black;"> Zone where the ECS instance is located </font></td>
    </tr>
    <tr>
        <td></td>
        <td><font style="color:black;">VPC ID</font></td>
        <td><font style="color:black;"> VPC where the resource is located </font></td>
    </tr>
    <tr>
        <td></td>
        <td><font style="color:black;"> Switch ID</font></td>
        <td><font style="color:black;"> Switch where the resource is located </font></td>
    </tr>
    </tbody>
</table>

<h2> Deployment process </h2>

<ol>
    <li> visit the compute nest LobeChat<a
            href="https://computenest.console.aliyun.com/service/instance/create/default?type=user&ServiceName=LobeChat%E7%A4%BE%E5%8C%BA%E7%89%88">
        deployment link </a> and fill in the deployment parameters as prompted
    </li>
    <li> Fill in the instance parameters <img src="./1.png" alt=""/></li>
    <li> Fill in the network parameters and click "Next: Confirm Order" <img
      src="./2.png" alt=""/></li>
    <li> Click Create Now and wait for the service instance deployment to complete <img 
                                                                                     src="./3.png" alt=""/>
    </li>
    <li> After the service instance is deployed, click the instance ID to go to the details page <img src="./img-en/serviceInstance1.png" alt=""/>
    </li>
    <li> Use the URL to access the service instance <img src="./img-en/serviceInstance2.png" alt=""/></li>
    <li> Go to the LobeChat application interface and configure the API Key<img src="./img-en/lobechat.png" alt=""/></li>
    for the model.
    <li> Get started with LobeChat!</li>
</ol>
