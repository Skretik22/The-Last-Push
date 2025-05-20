using UnityEngine;
using UnityEngine.UI;



public class PlayerController : Entity
{

    public float speed;
    public float jumpForce;
    public float normalSpeed;
    [SerializeField] private int lives = 3;
    private int health;

    [SerializeField] private Image[] hearts;

    [SerializeField] private Sprite aliveHeart;
    [SerializeField] private Sprite deadHeart;

    [SerializeField] private float _forceRebund = 5f;

    private Rigidbody2D rb;
    



    private bool isGrounded;
    public Transform feetPos;
    public float checkRadius;
    public LayerMask whatIsGround;

    [SerializeField] private Animator anim;


    public static PlayerController instance;


    public GameObject panel;







    private void Start()
    {
        speed = 0f;
        rb = GetComponent<Rigidbody2D>();

    }

    private void Awake()
    {
        instance = this;
        lives = 3;
        health = lives;
    }


    private void FixedUpdate()
    {
        if (speed != 0f)
        {
            rb.velocity = new Vector2(speed, rb.velocity.y);
            anim.SetBool("IsRunning", true);
        }

    }

    private void Update()
    {
        isGrounded = Physics2D.OverlapCircle(feetPos.position, checkRadius, whatIsGround);
        anim.SetBool("IsJumping", !isGrounded);

       


        if (health > lives)
            health = lives;

        for (int i = 0; i < hearts.Length; i++)
        {
            if (i < health)
                hearts[i].sprite = aliveHeart;
            else
                hearts[i].sprite = deadHeart;

            if (i < lives)
                hearts[i].enabled = true;
            else
                hearts[i].enabled = false;
        }



        if (lives <= 0)
        {
            panel.SetActive(true);
            gameObject.SetActive(false);
        }



    }

    public void OnJumpButtonDown()
    {
        if (isGrounded == true)
        {
            rb.AddForce(Vector2.up * jumpForce, ForceMode2D.Force);
            
        }
    }

    public void OnLeftButtonDown()
    {
        if (speed >= 0f)
        {
            speed = -normalSpeed;
            transform.eulerAngles = new Vector3(0, 180, 0);
        }
    }

    public void OnRightButtonDown()
    {
        if (speed <= 0f)
        {
            speed = normalSpeed;
            transform.eulerAngles = new Vector3(0, 0, 0);
        }
    }

    public void OnButtonUp()
    {
        speed = 0f;
        anim.SetBool("IsRunning", false);
        rb.velocity = new Vector2(0f, rb.velocity.y);
    }



    public void GetDamage(int damage)
    {
        
        rb.AddForce(new Vector2(-1f, 1f) * _forceRebund, ForceMode2D.Impulse);
        lives -= damage;
        Debug.Log(lives);
    }
}
