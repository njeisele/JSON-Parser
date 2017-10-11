# JSON-Parser

////////////Unity Part//////////////
using System.Collections;
using System.Collections.Generic;
using UnityEngine;


public class JSONparser : MonoBehaviour {

	// Use this for initialization
	void Start () {
	
        	
	}
	
    /*A JSON parser class for the current joint
      
      Holds the name of the joint, its position and quaternion */
    public class CurrentJointInfo
    {
        public string name { get; set; }
        public List<float> pos { get; set; }
        public List<float> rot { get; set; }
    }


    /* Python script should have message in form of 
    "{"name": "joint1", "pos":[1.2, 3.5, 5.6], "rot":[2.3, 4.5, 5.6, 8.7]};...
    */


    // Update is called once per frame
    void Update () {
        
        string message = wsc.messages[topic]; //get newest robot state data (from transform) //FINE

        string[] tfElements = message.Split(';'); //split the message into each joint/link data pair //STILL NEED TO SPLIT
        //Debug.Log(string.Join(", ", tfElements));
        foreach (string tfElement in tfElements)    //STILL LOOP
        {
            
            //Debug.Log(tfElement);
            var currentJoint = JsonUtility.FromJson<CurrentJointInfo>(tfElement);   //may work
            GameObject cur = GameObject.Find(currentJoint.name + "Pivot"); // replace with hashmap
            if (cur != null)
            {
                Vector3 curPos = new Vector3(currentJoint.pos[0], currentJoint.pos[1], currentJoint.pos[2]); //save current position
                Quaternion curRot = new Quaternion(currentJoint.rot[0], currentJoint.rot[1], currentJoint.rot[2], currentJoint.rot[3]); // save rot

                cur.transform.position = Vector3.Lerp(scale * RosToUnityPositionAxisConversion(curPos), cur.transform.position, 0.7f); //convert ROS coordinates to Unity coordinates and scale for position vector
                cur.transform.rotation = Quaternion.Slerp(RosToUnityQuaternionConversion(curRot), cur.transform.rotation, 0.7f); //convert ROS quaternions to Unity quarternions
                if (!cur.name.Contains("kinect"))
                { //rescaling direction of kinect point cloud
                    cur.transform.localScale = new Vector3(scale, scale, scale);
                }
                else {
                    cur.transform.localScale = new Vector3(-scale, scale, -scale);
                }
                //cur.transform.position = RosToUnityPositionAxisConversion (curPos);
                //cur.transform.rotation = RosToUnityQuaternionConversion (curRot);
            }
        }
    }
//////////////////Python Part/////////////////
def message_builder():
    """
    Builds the message to send on the ROS network to the VR computer
    Returns:
        msg (string): the message
    """
    msg = ""
    k = 'hi'
    trans = [3.6, 6.7, 2.1]
    rot = [1.6, 6.4, 8.6, 1.2]
        #msg += '{}:{}^{};'.format(k, trans, rot)
    msg += '{'
    msg += 'name:\"{}\",pos:{},rot:{}'.format(k, trans, rot)
    msg+= '};'
    # remove spaces to save on space
    print (msg)
    return msg.replace(' ', '')


def main():
    print ("hello")

main()
message_builder()
















