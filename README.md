using UnityEngine;
using UnityEngine.Networking;
using System.Collections;



public class PlayerMovements : NetworkBehaviour 
{
	
	public float speed ;
	public int force;
	
	public Transform checkSol;
	bool touchSol = false;
	float rayonSol = 0.3f;
	public LayerMask sol;

	[SyncVar]
	private Vector2 SyncPosition;
    private Vector2 SyncRotation;
	
	
	void FixedUpdate()
	{
		touchSol = Physics2D.OverlapCircle (checkSol.position, rayonSol, sol);
	}
	
	
	void Update () 
	{
		if (isLocalPlayer) {
			float x = Input.GetAxis ("Horizontal");
			
			if (touchSol && Input.GetKeyDown (KeyCode.Space)) {			
				GetComponent<Rigidbody2D> ().AddForce (new Vector2 (0, force * 10000)); 
				
			}
			
			
			if (x > 0) {
				transform.Translate (x * speed * Time.deltaTime, 0, 0);
				transform.eulerAngles = new Vector2 (0, 0);
				
			}
			
			if (x < 0) {
				transform.Translate (-x * speed * Time.deltaTime, 0, 0);
				transform.eulerAngles = new Vector2 (0, 180);
				
			}

			TransmitPosition ();
        } else {
			transform.position = Vector2.Lerp(transform.position, SyncPosition, Time.deltaTime * 15);
		}

	}

	[Command]
	void CmdSendMyPositionToTheServer(Vector2 positionReceived)
	{
		SyncPosition = positionReceived;
	}

    

    void TransmitPosition()
	{
		CmdSendMyPositionToTheServer (transform.position);
	}


}













