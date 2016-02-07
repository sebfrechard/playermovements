using UnityEngine;
using UnityEngine.Networking;
using System.Collections;

public class MyMovingObjectControllers : NetworkBehaviour
{
    public float speed;
    public int force;

    public Transform checkSol;
    bool touchSol = false;
    float rayonSol = 0.3f;
    public LayerMask sol;

    [SyncVar]
    Vector2 position;

    [SyncVar]
    float orientation;

    [Command]
    void CmdSetPositionAndOrientation(Vector2 position, float orientation)
    {
        this.position = position;
        this.orientation = orientation;
    }

    Vector2 translationVelocity;
    float angularVelocity;

    public float smoothTime = 0.1f;

    public void Update()
    {
        if (isLocalPlayer)
        {
            float x = Input.GetAxis("Horizontal");

            if (touchSol && Input.GetKeyDown(KeyCode.Space))
            {
                GetComponent<Rigidbody2D>().AddForce(new Vector2(0, force * 10000));

            }


            if (x > 0)
            {
                transform.Translate(x * speed * Time.deltaTime, 0, 0);
                transform.eulerAngles = new Vector2(0, 0);

            }

            if (x < 0)
            {
                transform.Translate(-x * speed * Time.deltaTime, 0, 0);
                transform.eulerAngles = new Vector2(0, 180);

            }

            // Send to the server.
            CmdSetPositionAndOrientation(position, orientation);
        }

        // Update the transform to reach the target.
        //transform.localPosition = position;
        //transform.localRotation = Quaternion.AngleAxis(orientation, Vector3.forward);

        // Update the transform to *smoothly* reach the target.
        transform.localPosition = Vector2.SmoothDamp(transform.localPosition, position, ref translationVelocity, smoothTime);
        transform.localRotation = Quaternion.AngleAxis(
            Mathf.SmoothDampAngle(transform.localRotation.eulerAngles.z, orientation, ref angularVelocity, smoothTime),
            Vector3.forward);
    }

}
